## Prometheus 服务发现

__基于文件的服务发现__
- 在 Prometheus.yml 配置文件下添加文件发现
    ```
    - job_name: 'k8s'
      basic_auth:
          username: prometheus
          password: 123456
      file_sd_configs:
      - files: ['/opt/monitor/prometheus/config/*.yml']
        refresh_interval: 5s # 每隔5秒检查一次
    ```
- 在 `file_sd_configs` 指定的文件路径下配置 yml 文件以指定监控目标，因为是 node_exporter 采集信息，所以只需要配置 `targets`
    ```
    - targets: ['192.168.1.202:9100','192.168.1.203:9100','192.168.1.204:9100']
    ```

__基于 Consul 的服务发现__
- 下载 [consul 执行文件](https://releases.hashicorp.com/consul/1.9.1/consul_1.8.5_linux_amd64.zip)
- 将执行文件放在 /opt/monitor/consul/ 路径下
- 创建 service 文件启动
    ```
    [Unit]
    Description=prometheus

    [Service]
    ExecStart=/opt/monitor/consul/consul agent -dev -ui -node=consul-dev -client=192.168.1.201
    ExecReload=/bin/kill -HUP $MAINPID
    KillMode=process
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target
    ```
- 启动 Consul 后访问本地 8500 端口即可访问页面
- 向 consul 添加 service ，将所需监控端写入 consul
    ```
    curl -X PUT -d '{"id": "frp","name": "esxi_node","address": "192.168.1.201","port":9100,"tags": ["service"],"checks": [{"http": "http://192.168.1.201:9100","interval":"5s"}]}' http://192.168.1.201:8500/v1/agent/service/register
    ```
    ```
    curl -X PUT -d '{"id": "harbor","name": "esxi_node","address": "192.168.1.205","port":9100,"tags": ["service"],"checks": [{"http": "http://192.168.1.205:9100","interval":"5s"}]}' http://192.168.1.201:8500/v1/agent/service/register
    ```
    - 删除 service
        ```
        curl -X PUT http://192.168.1.201:8500/v1/agent/service/deregister/esxi_node
        ```
- 在 Prometheus.yml 配置文件下添加 consul 的服务发现
    ```
  - job_name: 'esxi_node'
    basic_auth:
      username: prometheus
      password: 123456
    consul_sd_configs:
    - server: 192.168.1.201:8500
      services: ['esxi_node']
    ```