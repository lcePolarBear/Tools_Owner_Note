## 部署步骤
- 在本地[获取 node_exporter 二进制文件](https://github.com/prometheus/node_exporter/releases/download/v1.0.1/node_exporter-1.0.1.linux-amd64.tar.gz)
- 执行命令
    ```
    ansible-playbook -i hosts playbook.yaml
    ```