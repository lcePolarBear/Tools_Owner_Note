## 二进制
`cat /etc/redhat-release` 显示系统内核版本
`mv prometheus-2.6.1.linux-amd64 /usr/local/prometheus` 将软件放到对应的位置
prometheus.yml 是配置文件
在启动时有两个参数要注意
    `--storage.tsdb.path="data/"`   数据存储目录
    `--storage.tsdb.retention=15d`  默认保存15天
`./prometheus --config.file=prometheus.yml` 启动进程

将 Prometheus 配置为系统服务
使用 systemd 管理
cd /usr/lib/systemd/system
cp sshd.service prometheus.service
```
[Unit]
Description=https://prometheus.io/

[Service]
Restart=on-failure
ExecStart=/usr/local/prometheus/prometheus --config.file=/usr/local/prometheus/prometheus.yml

[Install]
WantedBy=multi-user.target
```
`systemctl daemon-reload`
`systemctl start prometheus`

## Docker
