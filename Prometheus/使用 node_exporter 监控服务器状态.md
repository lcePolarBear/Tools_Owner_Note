## 使用 Prometheus 监控多台服务器

__在被监控端的服务器部署 node_exporter__
- 获取[二进制安装包](hhttps://github.com/prometheus/node_exporter/releases/download/v1.0.1/node_exporter-1.0.1.linux-amd64.tar.gz)
- 解压压缩包并放入 /usr/local/node_exporter/ 路径下
- 创建 service 服务
    ```
    [Unit]
    Description=node_exporter

    [Service]
    ExecStart=/usr/local/node_exporter/node_exporter --web.config=/usr/local/node_exporter/config.yml
    ExecReload=/bin/kill -HUP $MAINPID
    KillMode=process
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target
    ```

__在 Prometheus.yml 下创建采集 node_exporter 的 job__
```
- job_name: 'Frp'
  static_configs:
  - targets: ['localhost:9100']
```
- 检查配置文件格式是否正常
    ```
    ./promtool check ./prometheus.yml
    ```
- 使 prometheus 重新读取配置文件而无需重启
    ```
    kill -HUP pid
    ```

__启用 node_exporter 接口 http 的认证__
- 安装密码加密工具 `httpd-tools`
- 执行加密
    ```
    htpasswd -nBC 12 '' | tr -d ':\n'
    ```
- 在 node_exporter 程序路径下创建配置文件 `config.yml` ，并将加密后的密码填入
    ```
    basic_auth_users:
      prometheus: xxxxxxxxx
    ```
- 重新启动 node_exporter
- 在 prometheus.yml 配置文件的 node_exporter job 下添加认证信息
    ```
    - job_name: 'Frp'
        basic_auth:
          username: prometheus
          password: 123456
        static_configs:
        - targets: ['192.168.1.201:9100','192.168.1.202:9100','192.168.1.203:9100']
    ```
- Grafana 使用仪表盘 9276 来可视化

__使用 Docker 的方式启动__
```
docker run -d \
  --net="host" \
  --pid="host" \
  -v "/:/host:ro,rslave" \
  quay.io/prometheus/node-exporter \
  --path.rootfs /host
````
