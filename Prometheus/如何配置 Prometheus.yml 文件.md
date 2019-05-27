## 如何配置 Prometheus.yml 文件
__安装 Prometheus 时编辑的 yml 文件详解__
```
global: #全局变量
  scrape_interval:     15s #抓取间隔为15秒
  evaluation_interval: 15s #规则评估间隔为15秒
  external_labels:
      monitor: 'codelab-monitor'

rule_files: #规则配置

scrape_configs: #抓取配置
  - job_name: 'prometheus' #设置抓取的任务名prometheus
    static_configs: #静态目标配置
      - targets: ['localhost:9090']
  - job_name: 'linux' #设置抓取的任务名linux
    static_configs: #静态目标配置,配置了该任务要抓取的所有实例
      - targets: ['192.168.56.12:9100'] #目标地址列表，地址由主机+端口组成
        labels: #标签列表
          alias: linux-node2
```
* global 在这里面定义的变量都是以默认值供 scrape_configs 使用
* 补充一下以上在 scrape_configs 中没有提到的常用配置
    * scrape_timeout:15s #抓取超过的时间间隔
    * scheme:http #协议，默认为 http，可选 https
    * metrics_path:/pathA/pathB/metrics #抓取地址的路径，联动 params
    * params:[ pathA:/data, pathB:/app ] #设定抓取地址的参数，联动 metrics_path
    * honor_labels:false #是否尊重抓取回来的标签，默认为 false
        * 当抓取回来的采样值的标签值跟服务端配置的不一致时，如果该配置为 true 则以抓取回来的为准。否则以服务端的为准，抓取回来的值会保存到一个新标签下，该新标签名在原来的前面加上了"exported_" 比如 exported_job
    * sample_limit:0 #单次抓取的采样值个数限制，默认为 0，表示没有限制