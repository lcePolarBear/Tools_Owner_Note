## 安装 Grafana
> [Grafana 官方说明文档](https://grafana.com/docs/)

__二进制安装__
- [安装包下载地址](https://dl.grafana.com/oss/release/grafana-7.2.2.linux-amd64.tar.gz)
- 解压并放入 /opt/monitor/prometheus/ 路径下
- 创建 service 文件启动
    ```
    [Unit]
    Description=grafana

    [Service]
    ExecStart=/opt/monitor/grafana/bin/grafana-server -homepath=/opt/monitor/grafana
    ExecReload=/bin/kill -HUP $MAINPID
    KillMode=process
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target
    ```
- 启动 Grafana 后访问本地 3000 端口即可访问页面

__Docker 安装__
> [官方 Grafana for Dcoker 说明指南](https://grafana.com/docs/installation/docker/)
- 先新建一个文件夹并赋予 777 的权限
    ```
    chmod 777 -R /app/yw/grafana-storage
    ```
- 启动 docker
    ```
      docker run \
      -d \
      -p 3000:3000 \
      --name=grafana \
      -v /app/yw/grafana-storage:/var/lib/grafana \
      grafana/grafana
    ```