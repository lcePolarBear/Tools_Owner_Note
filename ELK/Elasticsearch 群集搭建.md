# Elasticsearch 群集搭建

Elasticsearch（简称ES）是一个分布式、RESTful 风格的搜索和数据分析引擎，用于集中存储日志数据。

| Elasticsearch 术语 | 说明 | 对应关系型数据库结构 |
| --- | --- | --- |
| Index | Index 是多个 Document 的合集 | Database |
| Type | 一个 Index 可以定义一种或多种类型，将 Document 逻辑分组 | Table |
| Document | Index 里的每条记录称为 Document ，若干文档构建一个 Index | Row |
| Faild | ES 存储的最小单元 | Column |

es 安装包下载路径

[https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.9.3-linux-x86_64.tar.gz](https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.9.3-linux-x86_64.tar.gz)

### 修改系统内核参数

```bash
# 调整进程最大打开文件数数量
echo "* hard nofile 65535" >> /etc/security/limits.conf
echo "* soft nofile 65535" >> /etc/security/limits.conf

# 调整进程最大虚拟内存区域数量
echo "vm.max_map_count=262144" >> /etc/sysctl.conf
sysctl -p

# 重启生效
reboot
```

### es 配置文件

```bash
# sed -e '/^#/d' config/elasticsearch.yml | more

cluster.name: elk-cluster # 集群名称
node.name: node-1 # 集群节点名称
network.host: 0.0.0.0 # 监听地址
http.port: 9200 # 监听端口
discovery.seed_hosts: ["192.168.2.211", "192.168.2.212"] # 集群节点列表
cluster.initial_master_nodes: ["node-1"] # 首次启动指定的 master 节点

# 非 master 节点不启用cluster.initial_master_nodes参数，注释掉
```

### 创建 service 管理

```bash
# cat /usr/lib/systemd/system/elasticsearch.service

[Unit]
Description=elasticsearch

[Service]
User=es
LimitNOFILE=65535
ExecStart=/opt/elk/elasticsearch/bin/elasticsearch
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure

[Install]
WantedBy=multi-user.target

# systemctl daemon-reload 
# systemctl start elasticsearch
# systemctl status elasticsearch
```

### 查看状态

```bash
# 查看集群节点
curl -XGET 'http://127.0.0.1:9200/_cat/nodes?pretty'

# 查询集群状态
curl -i -XGET 'http://127.0.0.1:9200/_cluster/health?pretty'
```