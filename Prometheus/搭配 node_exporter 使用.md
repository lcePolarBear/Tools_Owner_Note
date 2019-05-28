## node_exporter 是一个可以将 CPU 内存等数据提供给 Prometheus 来展示的部件

[下载地址](https://prometheus.io/download/)

解压
```
tar xvfz node_exporter-x.x.x.linux-amd64
```

然后到 Prometheus.yml 文件中配置上 node_exporter 的节点
```
scrape_configs:

  - job_name: 'node'
    static_configs:
    - targets: ['127.0.0.1:9100']
```
启动后就可以在 Prometheus 的监控界面看到 node_ 节点了
```
./node_exporter
```