## node_exporter 是一个可以将 CPU 内存等数据提供给 Prometheus 来展示的部件
__用安装包的方式启动__

[安装包下载地址](https://prometheus.io/download/)

解压
```
tar xvfz node_exporter-x.x.x.linux-amd64
```
启动后就可以在 Prometheus 的监控界面看到 node_ 节点了
```
./node_exporter
```

__使用 Docker 的方式启动__
```
docker run -d \
  --net="host" \
  --pid="host" \
  -v "/:/host:ro,rslave" \
  quay.io/prometheus/node-exporter \
  --path.rootfs /host
  ````

__启动后需要在 Prometheus.yml 下配置才能在 Prometheus 中生效__
```
scrape_configs:

  - job_name: 'node'
    static_configs:
      - targets: ['127.0.0.1:9100']
```