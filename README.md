# 此仓库专门用来记录运维中使用到的工具

__Ansible__
- [Ansible 的安装与配置](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Ansible/Ansible%20%E7%9A%84%E5%AE%89%E8%A3%85%E4%B8%8E%E9%85%8D%E7%BD%AE.md)
- [Ansible ad-hoc 模式的使用](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Ansible/Ansible%20ad-hoc%20%E6%A8%A1%E5%BC%8F%E7%9A%84%E4%BD%BF%E7%94%A8.md)
- [Ansible 的常用模块](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Ansible/Ansible%20%E7%9A%84%E5%B8%B8%E7%94%A8%E6%A8%A1%E5%9D%97.md)
- __Playbook__
    - [Playbook 的使用](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Ansible/Playbook%20%E7%9A%84%E4%BD%BF%E7%94%A8.md)
    - [Playbook 定义变量与使用](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Ansible/Playbook%20%E5%AE%9A%E4%B9%89%E5%8F%98%E9%87%8F%E4%B8%8E%E4%BD%BF%E7%94%A8%20.md)
    - [Playbook 文件复用](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Ansible/Playbook%20%E6%96%87%E4%BB%B6%E5%A4%8D%E7%94%A8.md)
    - [Playbook 流程控制](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Ansible/Playbook%20%E6%B5%81%E7%A8%8B%E6%8E%A7%E5%88%B6.md)
    - [jinja2 - Playbook 模板](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Ansible/jinja2%20-%20Playbook%20%E6%A8%A1%E6%9D%BF.md)
    - [角色 - Roles](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Ansible/%E8%A7%92%E8%89%B2%20-%20Roles.md)
- __最佳实践__
    - [自动部署 Web 服务器](https://github.com/lcePolarBear/Ops_Automation_Note/tree/master/Ansible/%E6%A1%88%E4%BE%8B%EF%BC%9A%E8%87%AA%E5%8A%A8%E9%83%A8%E7%BD%B2%20Web%20%E6%9C%8D%E5%8A%A1%E5%99%A8)
    - [批量部署 node_exporter]()

__Prometheus + Grafana 监控__
- [安装 Prometheus](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Prometheus/%E5%AE%89%E8%A3%85%20Prometheus.md)
- [配置 Prometheus.yml](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Prometheus/如何配置%20Prometheus.yml%20文件.md)
- [使用 node_exporter 监控服务器状态](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Prometheus/%E4%BD%BF%E7%94%A8%20node_exporter%20%E7%9B%91%E6%8E%A7%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%8A%B6%E6%80%81.md)
- __Prometheus 自动化监控__
    - [Prometheus 服务发现](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Prometheus/Prometheus%20%E6%9C%8D%E5%8A%A1%E5%8F%91%E7%8E%B0.md)
    - [使用 Prometheus 监控 kubernetes](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Prometheus/%E4%BD%BF%E7%94%A8%20Prometheus%20%E7%9B%91%E6%8E%A7%20kubernetes.md)
- [安装 Grafana](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Prometheus/Grafana/%E5%AE%89%E8%A3%85%20Grafana.md)
- [Grafana 接入 Pronetheus](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Prometheus/Grafana/Grafana%20%E6%8E%A5%E5%85%A5%20Prometheus.md)

__ESXI__
- [安装 EXSI 6.5 参考文档](https://i4t.com/2773.html)
- [安装 EXSI 6.5 遇到 No Network Adapters 的解决方案](https://www.dyxmq.cn/windows/software/vsphere-esxi-no-network-adapters.html)
- 安装完后用 Chrome 上传镜像文件不然会因为 ssl 证书问题无法上传
- [为 EXSI 6.5 生成 https 安全访问证书](https://docs.vmware.com/cn/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-EA0587C7-5151-40B4-88F0-C341E6B1F8D0.html)
- [为 ESXI 6.7 生成 https 安全访问证书](https://blog.csdn.net/kadwf123/article/details/108314038)
- [使用 frp 代理 ESXI 使其可以通过外网访问](https://blog.csdn.net/weixin_42318691/article/details/108396640)

__Git__
- [Git 基础指令](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/Git/Git%20%E5%9F%BA%E7%A1%80%E6%8C%87%E4%BB%A4.md)

__FRP__
- [FRP 使用说明](https://github.com/fatedier/frp/blob/master/README_zh.md)
- [使用 FRP 映射 Oracle 1521 端口](https://github.com/lcePolarBear/Ops_Automation_Note/blob/master/FRP/Oracle%201521端口映映射.md)
- [安全地暴露内网服务](https://gofrp.org/docs/examples/stcp/)