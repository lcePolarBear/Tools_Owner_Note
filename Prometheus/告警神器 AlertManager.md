## 部署 AlertManager
alertmanager.yml 配置文件
```
global:
  resolve_timeout: 5m   //超时时间

route:                  //告警的发送和分配
  group_by: ['alertname']   //分组依据
  group_wait: 10s           //分组等待时间
  group_interval: 10s       //分组间隔时间
  repeat_interval: 1h       //重复告警时间
  receiver: 'web.hook'
receivers:              //接收端信息
- name: 'web.hook'
  webhook_configs:
  - url: 'http://127.0.0.1:5001/'
inhibit_rules:          //告警收敛
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'warning'
    equal: ['alertname', 'dev', 'instance']
```
重新配置 alertmanager.yml
```
global:
  resolve_timeout: 5m
  smtp_smarthost: 'smtp.126.com:25'
  smtp_from: 'xxx@126.com'
  smtp_auth_username: 'xxx@126.com'
  smtp_auth_password: 'ZRWXCQXHKPPWJAGB'
  smtp_require_tls: false

route:
  group_by: ['alertname']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 1m
  receiver: 'mail'
receivers:
- name: 'mail'
  email_configs:
  - to: '1198502445@qq.com'
#inhibit_rules:
#  - source_match:
#      severity: 'critical'
#    target_match:
#      severity: 'warning'
#    equal: ['alertname', 'dev', 'instance']
```
检查配置文件
`./amtool check-config alertmanager.yml`

运行 alertmanager
`./alertmanager --config.file=alertmanager.yml`

### 配置 Prometheus 与 Altermanager 通信
配置 prometheus.yml 的告警信息，创建告警规则
```
# Alertmanager configuration
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      # - 127.0.0.1:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"
    - "rules/*.yml"
```
在 Prometheus 路径下创建 rules 目录并创建 test.yml 文件
```
groups:
- name: general
  rules:

  # Alert for any instance that is unreachable for >5 minutes.
  - alert: InstanceDown
    expr: up == 0
    for: 1m
    labels:
      severity: error
    annotations:
      summary: "Instance {{ $labels.instance }} 停止工作"
      description: "{{ $labels.instance }} of job {{ $labels.job }} 已经停止工作五分钟以上"
```
重启 prometheus 服务后生效

## 告警分配到指定组
通过设置 routes
```
route:
  receiver: 'default-receiver'
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 4h
  group_by: [cluster, alertname]
routes:
- receiver: 'database-pager'
    group_wait: 10s
    match_re:
      service: mysql|cassandra
- receiver: 'frontend-pager'
    group_by: [product, environment]
    match:
      team: frontend
receivers:
- name: 'database-pager'
  email_configs:
  - to: 'zhenliang369@163.com'
- name: 'frontend-pager'
  email_configs:
  - to: 'zhenliang369@163.com'
```

## 告警收敛
### 分组
alertmanager.yml
```
route:
  group_by: ['alertname'] #根据标签进行分组
  group_wait: 10s         #发送告警等待时间（合并邮件用的）
  group_interval: 10s     #间隔发送邮件时间
  repeat_interval: 1m     #重复发送邮件间隔时间
```
作用：将类似的告警分类成单个通知

### 抑制
```
#inhibit_rules:
#  - source_match:
#      severity: 'critical'
#    target_match:
#      severity: 'warning'
#    equal: ['alertname', 'dev', 'instance']
```
当服务器宕机的告警和应用程序掉线的告警同时出现时，应用程序告警应该被抑制。因为服务器都宕机了，应用程序肯定掉线了

### 静默
简单的特定时间提醒的机制



## Prometheus 告警触发流程
Prometheus -> 触发阈值 -> 判断是否超出持续时间 -> Altermanager -> 分组/抑制/静默 -> 发送邮件

1. Prometheus.yml
```
global:
  scrape_interval:     15s # 将刷新间隔设置为每 15 秒一次。默认值为每1分钟
  evaluation_interval: 15s # 每 15 秒评估一次规则。默认值是每1分钟一次
  # scrape_timeout is set to the global default (10s).
```

2. prometheus/rules/test.yml
```
  # Alert for any instance that is unreachable for >5 minutes.
  - alert: InstanceDown
    expr: up == 0
    for: 1m #用来判断故障发生持续了多长时间才触发报警
    labels:
      severity: error
    annotations:
      summary: "Instance {{ $labels.instance }} 停止工作"
      description: "{{ $labels.instance }} of job {{ $labels.job }} 已经停止工作五分钟以上"
```

3. alertmanager-0.16.0.linux-amd64/alertmanager.yml
```
global: #用来配置发送邮箱的信息
  resolve_timeout: 5m
  smtp_smarthost: 'smtp.126.com:25'
  smtp_from: 'joen_chen@126.com'
  smtp_auth_username: 'joen_chen@126.com'
  smtp_auth_password: 'ZRWXCQXHKPPWJAGB'
  smtp_require_tls: false

route:  #用来配置分组的信息
  group_by: ['alertname'] #分组名
  group_wait: 10s         #分组信息收集等待时间
  group_interval: 10s     #分组之间发送信息的间隔
  repeat_interval: 1m     #重复发送信息间隔时间
  receiver: 'mail'
receivers:                #告警通知方信息
- name: 'mail'
  email_configs:
  - to: '1198502445@qq.com'
#inhibit_rules: #分组/抑制/静默
#  - source_match:
#      severity: 'critical'
#    target_match:
#      severity: 'warning'
#    equal: ['alertname', 'dev', 'instance']
```

## 编写告警规则案例
在 prometheus/rules 路径下复制 test.yml 建一个 node.yml
```
groups:
- name: node.rules
  rules:
  - alert: Node I/O Usage
    expr: 100-node_filesystem_free_bytes{mountpoint="/",fstype=~"ext4|xfs"} / node_filesystem_size_bytes{fstype=~"ext4|xfs"}*100 > 80 #监控磁盘的使用率，达到 80% 触发报警
    for: 1m
    labels:
      severity: Warning
    annotations:
      summary: "Instance {{ $labels.instance }} : {{$labels.mountpoint}} error"
      description: "{{ $labels.instance }} now value: {{ $value }}"
  - alert: Memory Usage
    expr: 100-(node_memory_MemFree_bytes+node_memory_Cached_bytes+node_memory_Buffers_bytes)/node_memory_MemTotal_bytes > 80 #监控内存的使用率，达到 80% 触发报警
    for: 1m
    labels:
      severity: Warning
    annotations:
      summary: "Instance {{ $labels.instance }} : {{$labels.mountpoint}} error"
      description: "{{ $labels.instance }} now value: {{ $value }}"
  - alert: CPU Usage
    expr: 100-node_filesystem_free_bytes{mountpoint="/",fstype=~"ext4|xfs"} / node_filesystem_size_bytes{mountpoint="/",fstype=~"ext4|xfs"}*100 > 80 #监控 CPU 的使用率，达到 80% 触发报警
    for: 1m
    labels:
      severity: Warning
    annotations:
      summary: "Instance {{ $labels.instance }} : {{$labels.mountpoint}} error"
      description: "{{ $labels.instance }} now value: {{ $value }}"
```

重启 Prometheus 或者使用 kill -hup 命令使配置文件生效