## 如何安装 Grafana
[Grafana 官方说明文档](https://grafana.com/docs/)

__安装包安装__

 [rpm 安装包下载地址](https://grafana.com/grafana/download?platform=linux)

安装
```
sudo yum install grafana-x.x.x-1.x86_64.rpm
```
启动
```
systemctl start grafana-server
```
加入开机自启
```
sudo systemctl enable grafana-server.service
```
* /etc/sysconfig/grafana-server （文件存放着运行时的环境变量）
* /var/log/grafana/grafana.log （是 Grafana 的日志文件）
    * 在这里你可以覆盖日志目录，数据目录和其他变量
* /var/lib/grafana/grafana.db （Grafana sqllite 数据库）
* /etc/grafana/grafana.ini（Grafana 的配置文件）
    * [配置文件的说明](https://grafana.com/docs/installation/configuration/)

__Docker 安装__

[官方 Grafana for Dcoker 说明指南](https://grafana.com/docs/installation/docker/)

先新建一个文件夹并赋予 777 的权限
```
chmod 777 -R /app/yw/grafana-storage
```
启动 docker
```
  docker run \
  -d \
  -p 3000:3000 \
  --name=grafana \
  -v /app/yw/grafana-storage:/var/lib/grafana \
  grafana/grafana
```