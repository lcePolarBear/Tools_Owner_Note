# Filebeat 日志采集

Filebeat 是一个轻量级的日志采集器，将采集的数据推送到 Logstash、ES 存储

## 安装

### 采用 RPM 安装

```bash
rpm -ivh filebeat-7.9.3-x86_64.rpm
```

## 配置

### 采集指定日志

```bash
# vi /etc/filebeat/filebeat.yml

filebeat.inputs:

# 配置不同的输入
- type: log

    # 是否启用该输入配置
    enabled: false

    # 采集的日志文件路径，可以通配
    paths:
    - /var/log/*.log

    # 正则匹配要排除的行，这里以DBG开头的行都过滤掉
    #exclude_lines: ['^DBG']

    # 正则匹配要采集的行，这里以ERR/WARN开头的行都采集
    #include_lines: ['^ERR', '^WARN']

    # 排除的文件，默认采集所有
    #exclude_files: ['.gz$']

    # 添加标签
    #tags: ["nginx"]

    # 下面fields添加的字段默认是在fields.xxx，可以设置在顶级对象下
    # fields_under_root: true
    # 自定义添加的字段，一般用于标记日志来源
    #fields:
    # level: debug
    # review: 1
```

### 推送到 Logstash 或者 ES

```bash
output.logstash:
  hosts: ["192.168.2.211:5044"]

###

setup.ilm.enabled: false
setup.template.name: "microservice-product"
setup.template.pattern: "microservice-product-*"
output.elasticsearch:
  hosts: ["localhost:9200"]
  index: "microservice-product-%{+yyyy.MM.dd}"
```

### 示例

```bash
# vi /etc/filebeat/filebeat.yml

filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /var/log/test/*.log
  tags: ["nginx"]
  fields_under_root: true
  fields:
    project: microservice
    app: product

output.logstash:
  hosts: ["192.168.2.211:5044"]

# vi /opt/logstash/conf.d/text.conf

input{
    beats {
        host => "0.0.0.0"
        port => 5044
    }
}
filter {
    if [app] in ["test","product"] and [project] =="microservice" {
        mutate {
            add_field => {
                "[@metadata][target_index]" => "test-%{+YYYY.MM}"
            }
        }
    } else {
        mutate {
            add_field => {
                "[@metadata][target_index]" => "other-%{+YYYY}"
            }
        }
    }
}
output{
    elasticsearch {
        hosts => ["192.168.2.211:9200","192.168.2.212:9200"]
        index => "%{[@metadata][target_index]}"
    }
}
```