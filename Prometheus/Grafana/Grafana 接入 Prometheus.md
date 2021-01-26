## Grafana 接入 Prometheus

__设定数据源__
- 在 Configuration -> Data Sources 添加 Prometheus
- 只设定 url 为 http://IP:9090 就可以 [sava & Teest]

__设定 dashboard__
- 在[dashboard 官方网站](https://grafana.com/dashboards)有着大量优秀的仪表盘，可以通过各种条件选择适合自己的仪表盘
- 以 [8919](https://grafana.com/dashboards/8919) 为例，选择 Create -> import 将 8919 写到输入栏
- 一会儿系统会索引到这个仪表盘模板 选择 import 就可以使用了