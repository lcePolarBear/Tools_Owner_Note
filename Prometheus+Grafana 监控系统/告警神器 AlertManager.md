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