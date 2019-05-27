node_exporter是一个可以将 CPU 内存等数据提供给Prometheus来展示的部件

[下载地址](https://prometheus.io/download/)

解压并启动
```
tar xvfz node_exporter-x.x.x.linux-amd64
./node_exporter
```
这样 Prometheus 就可以采集系统自身的信息