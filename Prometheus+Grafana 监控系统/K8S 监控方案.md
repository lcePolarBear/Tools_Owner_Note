## 监控指标
### k8s 本身监控
- node 数量
- node 资源利用率
- Pods数量 (node)
- 资源对象状态

### Pod 监控
- Pod 数量（项目）
- 容器资源利用率
- 应用程序

## 实现思路

- Pod 性能 - cadvisor - 容器 cpu ，内存利用率
- Node 性能 - node-exporter - 节点 cpu ，内存利用率
- K8S 资源对象 - kube-state-metrics - Pod/Deployment/Service

