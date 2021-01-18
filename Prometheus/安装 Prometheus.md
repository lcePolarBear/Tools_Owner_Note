## 安装 Prometheus （运行系统： CentOS:7 ）

__使用安装包来运行__
- 获取[二进制安装包](https://github.com/prometheus/prometheus/releases/download/v2.22.0/prometheus-2.22.0.linux-amd64.tar.gz)
- 解压压缩包并放入 /opt/monitor/prometheus/ 路径下，可以看到文件列表有
    - 二进制启动项 `prometheus`
    - 配置文件 `prometheus.yml`
- 创建 service 服务
    ```
    [Unit]
    Description=prometheus

    [Service]
    ExecStart=/opt/monitor/prometheus/prometheus --config.file=/opt/monitor/prometheus/prometheus.yml
    ExecReload=/bin/kill -HUP $MAINPID
    KillMode=process
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target
    ```
- prometheus.yml 文件的[配置说明](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Prometheus/如何配置%20Prometheus.yml%20文件.md)
- 访问 web 界面： localhost:9090

__使用 Docker 来运行__
- 获取并启动 prometheus：
```
docker run  -d \
  -p 9090:9090 \
  -v /app/yw/prometheus.yml:/etc/prometheus/prometheus.yml  \
  quay.io/prometheus/prometheus
```
- 通过 IP地址:9090 来访问容器内的 Prometheus 服务