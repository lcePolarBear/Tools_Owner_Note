## 基于 kubernetes 构建 Jenkins 持续集成平台
---
### Jenkins 在 kubernetes 的部署
__准备工作__ _[参考链接](https://github.com/lcePolarBear/Docker_Basic_Config_Note/blob/master/Dcoekr%20%E5%AE%9E%E4%BE%8B/%E5%9F%BA%E4%BA%8E%20Docker%20%E6%9E%84%E5%BB%BA%20Jenkins%20CI%20%E5%B9%B3%E5%8F%B0.md)_
1. 部署 harbor 仓库并在 kubernetes 的 work 节点配置 harbor 仓库地址  
2. 创建 git 仓库

__创建 NFS 并配置自动分配 PV 的 StorageClass__
1. 创建并配置 NFS ，确保能挂载 NFS 路径 _[参考链接](https://github.com/lcePolarBear/Kubernetes_Basic_Config_Note/blob/master/%E4%BD%BF%E7%94%A8%E6%8C%87%E5%8D%97/Kubernetes%20%E5%AD%98%E5%82%A8.md)_
2. 部署 rbac _[rbac.yaml](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Jenkins/nfs-client/rbac.yaml)_
3. 部署  StorageClass _[class.yaml](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Jenkins/nfs-client/class.yaml)_
4. 创建自动分配 PV 的 Pod _[deployment.yaml](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Jenkins/nfs-client/deployment.yaml)_

__部署 jenkins__
1. 部署 rbac _[service-account.yml](https://github.com/jenkinsci/kubernetes-plugin/blob/master/src/main/kubernetes/service-account.yml)_
2. 部署 Jenkins _[jenkins.yml](https://github.com/jenkinsci/kubernetes-plugin/blob/master/src/main/kubernetes/jenkins.yml)_
    - 创建 StatefulSet 时在 volumeClaimTemplates 里指定 nfs 设定的 storageClassName
    - 如果没有部署 ingress ，创建 Service 时可以使用 NodePort 方式对外暴露服务

### Jenkins 在 kubernetes 创建动态代理 jenkins-agent
1. 首先在 Jenkins 安装插件 _kubernetes_
2. 配置 Jenkins cloud 信息
    - 注意 Jenkins 地址和 Jenkins 通道都要配置， Jenkins 通道可能因为 bug ，目前只能以 ip:port 格式填入才生效
3. 暂时不用配置 pod 模板
4. pipeline 测试能否创建 jenkins-agent
    ```json
    podTemplate(label: 'jenkins-slave', cloud: 'kubernetes', containers: [
        containerTemplate(
            name: 'jnlp', 
            image: "jenkins/inbound-agent"
        )
      ]
    )
    {
      node("jenkins-slave"){
        stage('测试'){
          sh "sleep 3600"
        }
      }
    }
    ```

### Jenkins 在 kubernetes 实现 CI
__Jenkins CI 部署流程__
1. 使用 git 拉取项目代码
2. 通过 maven 构建项目
3. 通过 docker 构建镜像并推送到 harbor

__准备工作__
- 向 jenkins/inbound-agent 镜像添加 maven 后重新构建
    ```Dockerfile
    # vi Dockerfile
    FROM jenkins/inbound-agent 
    USER root

    RUN apt update
    RUN apt install maven -y

    ENTRYPOINT ["/usr/local/bin/jenkins-agent"]

    # docker build -t 192.168.1.221/jenkins/inbound-agent:maven .
    # docker push 192.168.1.221/jenkins/inbound-agent:maven
    ```

__使用 pipeline 实施 CI__
```json
podTemplate(label: 'jenkins-slave', cloud: 'kubernetes', containers: [
    containerTemplate(
        name: 'jnlp', /* jnlp 是必须的，用于对接 k8s 生成的 Pod */
        image: "192.168.1.221/jenkins/inbound-agent:maven"
    ), /* 镜像地址提取刚刚推送到 harbor 的镜像 */
  ],
  volumes: [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
    hostPathVolume(mountPath: '/usr/bin/docker', hostPath: '/usr/bin/docker')
  ], /* 将宿主机的 docker 路径挂载到容器中 */
) 
{
  node("jenkins-slave"){
      // 第一步
      stage('拉取代码'){
          /* 使用流水线语法构建 git 拉取项目的指令 */
      }
      // 第二步
      stage('代码编译'){
          sh "mvn clean package -Dmaven.test.skip=true"
      }
      // 第三步
      stage('构建镜像'){
          /* 在凭证中添加 harbor 的 username/password 全局凭证 docker_registry_auth */
          withCredentials([usernamePassword(credentialsId: "${docker_registry_auth}", passwordVariable: 'password', usernameVariable: 'username')]) {
            sh """
              echo '
                FROM lizhenliang/tomcat
                RUN rm -rf /usr/local/tomcat/webapps/*
                ADD target/*.war /usr/local/tomcat/webapps/ROOT.war
              ' > Dockerfile
              ls
              ls target
              docker build -t 192.168.1.221/library/demo:latest .
              docker login -u ${username} -p '${password}' 192.168.1.221
              docker push 192.168.1.221/library/demo
            """
            }
      }
```

### Jenkins 在 kubernetes 实现 CD
__Jenkins 安装 Kubernetes Continuous Deploy 插件__
- 使用流水线语法构建 kubernetesDeploy 语句

__创建 deployment.yaml__
- 向 git 仓库提交 deployment 部署脚本 _[deploy.yaml](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Jenkins/CI/CD/deploy.yaml)_
- CD 时随 git 一起拉取到 jenkins-agent 容器内

__在 k8s 创建 secret__
```bash
kubectl create secret docker-registry registry-pull-secret --docker-username=user --docker-password=password --docker-server=192.168.1.221
```

__使用 pipeline 实施 CD__
```json
podTemplate(label: 'jenkins-slave', cloud: 'kubernetes', containers: [
    containerTemplate(
        name: 'jnlp', 
        image: "192.168.1.221/jenkins/inbound-agent:maven"
    )
  ]
)
{
  node("jenkins-slave"){
      // 第一步
      stage('拉取代码'){
          /* 使用流水线语法构建 git 拉取项目的指令 */
      }
      // 第二步
      stage('部署到K8S平台'){
          /* 使用 192.168.1.221/library/demo:latest 替换 deploy.yaml 文件内的 $IMAGE_NAME 占位符 */
          /* 使用 registry-pull-secret 替换 deploy.yaml 文件内的 $SECRET_NAME 占位符 */
          sh """
          sed -i 's#\$IMAGE_NAME#192.168.1.221/library/demo:latest#' deploy.yaml
          sed -i 's#\$SECRET_NAME#registry-pull-secret#' deploy.yaml
          """
          /* 使用流水线语法构建的 kubernetesDeploy 语句 */
      }
  }
}
```