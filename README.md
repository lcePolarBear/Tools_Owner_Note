# 此仓库专门用来记录运维中使用到的工具

__Git__
- [Git 基础指令](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Git/Git%20%E5%9F%BA%E7%A1%80%E6%8C%87%E4%BB%A4.md)

__Prometheus__
- [安装 Prometheus](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Prometheus/%E5%AE%89%E8%A3%85%20Prometheus.md)
- [配置 Prometheus.yml](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Prometheus/如何配置%20Prometheus.yml%20文件.md)
- [使用 node_exporter 监控服务器状态](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Prometheus/%E4%BD%BF%E7%94%A8%20node_exporter%20%E7%9B%91%E6%8E%A7%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%8A%B6%E6%80%81.md)
- Prometheus 自动化监控
    - [Prometheus 服务发现](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Prometheus/Prometheus%20%E6%9C%8D%E5%8A%A1%E5%8F%91%E7%8E%B0.md)
    - [使用 Prometheus 监控 kubernetes](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Prometheus/%E4%BD%BF%E7%94%A8%20Prometheus%20%E7%9B%91%E6%8E%A7%20kubernetes.md)

__Grafana__
- [Grafana 官方文档](https://grafana.com/docs/)
- [安装 Grafana](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Grafana/%E5%AE%89%E8%A3%85%20Grafana.md)
- [Grafana 接入 Pronetheus](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Grafana/Grafana%20%E6%8E%A5%E5%85%A5%20Prometheus.md)

__Ansible__
- [Ansible 的安装与配置](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Ansible/Ansible%20%E7%9A%84%E5%AE%89%E8%A3%85%E4%B8%8E%E9%85%8D%E7%BD%AE.md)
- [Ansible ad-hoc 模式的使用](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Ansible/Ansible%20ad-hoc%20%E6%A8%A1%E5%BC%8F%E7%9A%84%E4%BD%BF%E7%94%A8.md)
- 最佳实践
    - [使用 Ansible 批量部署 node_exporter]()

__Jenkins__
- [Jenkins 官方文档](https://jenkins.io/zh/doc/) 以及 [Docker 部署方式](https://jenkins.io/zh/doc/book/installing/)

__FRP__
- [FRP 使用说明](https://github.com/fatedier/frp/blob/master/README_zh.md)
- [使用 FRP 映射 Oracle 1521 端口](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/FRP/Oracle%201521端口映映射.md)
- [安全地暴露内网服务](https://gofrp.org/docs/examples/stcp/)

__ESXI__
- [安装参考文档](https://i4t.com/2773.html)
- [安装 EXSI 6.5 遇到 No Network Adapters 的解决方案](https://www.dyxmq.cn/windows/software/vsphere-esxi-no-network-adapters.html)
- 安装完后用 Chrome 上传镜像文件不然会因为 ssl 证书问题无法上传
- [使用 mkcert 工具为 ESXI6.7 主机 https 生成安全访问证书](https://blog.csdn.net/kadwf123/article/details/108314038)
- [使用 frp 代理 esxi 使其可以通过外网访问](https://blog.csdn.net/weixin_42318691/article/details/108396640)