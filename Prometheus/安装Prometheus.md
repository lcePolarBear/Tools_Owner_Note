运行系统：CentOS:7

## 使用安装包来运行
下载安装包 [下载地址](https://prometheus.io/download/)

解压压缩包
```
tar xvfz prometheus-2.2.1.linux-amd64.tar.gz
```
可以看到文件列表有
* prometheus -> 二进制启动项
* prometheus.yml -> 配置文件

配置文件中有着默认的配置文件 我们可以替换成自己需要的
```
global:
  scrape_interval:     15s 
  evaluation_interval: 15s 
  external_labels:
      monitor: 'codelab-monitor'
rule_files:
scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
  - job_name: 'linux'
    static_configs:
      - targets: ['192.168.56.12:9100']
        labels:
          alias: linux-node2
```
关于以上配置文件的配置说明[请看此处](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Prometheus/如何配置Prometheus.yml文件.md)

直接联动 yml 配置文件启动二进制程序
```
./prometheus --config.file=prometheus.yml
```
访问web界面：IP地址:9090

## 使用 Docker 来运行
* 获取并启动 prometheus：
```
docker run -p 9090:9090 prom/prometheus
```
* 通过 IP地址:9090 来访问容器内的 Prometheus 服务