## 特点
- 多维数据模型
- PromSQL
- 不依赖分布式存储，单个服务器节点可直接工作
- 基于 HTTP 的 pull 方式采取时间序列数据
- 推送时间序列数据通过 PushGateway 组件支持
- 通过服务发现或静态配置发现目标
- 多种 UI 和仪表盘支持（ Grafana ）
## 组成及其架构
- Server （收集信息并提供查询窗口）
- 对短任务长任务的收集
- 对 k8s 的监控
- 支持 Grafana
- 支持报警
## 数据模型
- 时间序列格式 度量标准名称{一组键值对}  `api_thhp_requests_total{method="POST",handler="/messages"}`
## 指标类型
- Counter   递增的计数器（接口请求次数）
- Gauge     可以任意变化的数值（内存使用率的变化）
- Histogtam 对一段时间范围内的数据进行采样，并对所有的数值求和与统计数量
- Summary   类似 Histogtam
## 作业和实例
- 实例  可以抓取的目标称为实例
- 作业  具有相同目标的实例集合称为作业
