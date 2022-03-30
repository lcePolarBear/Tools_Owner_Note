# Logstash 日志结构化处理

Logstash能够将采集日志、格式化、过滤，最后将数据推送到Elasticsearch存储

- Input 输入：输入数据可以是 Stdin 、File 、TCP 、Redis 、Syslog 等
- Filter 过滤：
- Output 输出：

## Logstash 部署

### 二进制部署

```bash
yum install java-1.8.0-openjdk –y

```

```bash
# vi /usr/lib/systemd/system/logstash.service

[Unit]
Description=logstash

[Service]
ExecStart=/opt/logstash/bin/logstash
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

```bash
# sed -e '/^#/d' config/logstash.yml | more

pipeline: # 管道配置
	batch: 
		size: 125 
		delay: 5
path.config: /opt/logstash/conf.d # conf.d目录自己创建
# 定期检查配置是否修改，并重新加载管道。也可以使用SIGHUP信号手动触发
# config.reload.automatic: false
# config.reload.interval: 3s
# http.enabled: true
http.host: 0.0.0.0
http.port: 9600-9700
log.level: info
path.logs: /opt/logstash/logs
```

### Logstash 配置文件

```json
# cat /opt/logstash/conf.d/test.conf

input{

}
filter{

}
output{

}
```

## 输入插件

### Stdin

### File

```json
input {
    file {
        path => "/var/log/*.log"        # 日志文件路径，可使用通配符
        exclude => "boot.log"           # 排除采集的日志文件
        start_position => "beginning"   # 指定日志文件什么位置开始读，默认从结尾开始，指定beginning表示从头开始读
        tags => "web"                   # 添加任意数量的标签，用于标记日志的其他属性，例如表明访问日志还是错误日志
        tags => "nginx"
        type => "access"                # 为所有输入添加一个字段，例如表明日志类型
        add_field => {                  # 添加一个字段到一个事件，放到事件顶部，一般用于标记日志来源。例如属于哪个项目，哪个应用
            "project" => "microservice"
            "app" => "product"
        }
    }
}
```

### Redis

### Beats

接收来自Beats数据采集器发来的数据，例如Filebeat

```json
input {
    beats {
        host => "0.0.0.0"
        port => 5044
    }
}
```

## 过滤插件

过滤阶段：将日志格式化处理

### JSON

接收一个json数据，将其展开为Logstash事件中的数据结构，放到事件顶层

```json
filter{
    json {
        source => "message"     # 指定要解析的字段，一般是原始消息message字段
    }
}
```

模拟数据：`{"remote_addr": "192.168.1.10","url":"/index","status":"200"}`

### KV

接收一个键值数据，按照指定分隔符解析为 Logstash 事件中的数据结构，放到事件顶层

```json
filter{
    kv {
        field_split => "&?"     # 指定键值分隔符，可用于解析 URL 中的参数
    }
}
```

模拟数据：`www.ctnrs.com?id=1&name=aliang&age=30`

### Grok

如果采集的日志格式是非结构化的，可以写正则表达式提取，grok 是正则表达式支持的实现

Logstash内置的正则匹配模式，在安装目录下可以看到

```bash
cat /opt/logstash/vendor/bundle/jruby/2.5.0/gems/logstash-patterns-core-4.1.2/patterns/grok-patterns
```

```json
filter{
    grok {
        match => {
            "message" => "%{IP:client} %{WORD:method} %{URIPATHPARAM:request} %{NUMBER:bytes} %{NUMBER:duration}"
        }
    }
}
```

模拟数据：`192.168.1.10 GET /index.html 15824 0.043`

如果内置匹配模式中没有你想要的，可以自定义正则模式

```bash
# cat /opt/patterns
CID [0-9]{5,6}

---

filter{
    grok {
        patterns_dir =>"/opt/patterns"
        match => {
            "message" => "%{IP:client} %{WORD:method} %{URIPATHPARAM:request} %{NUMBER:bytes} %{NUMBER:duration} %{CID:cid}"
        }
    }
}
```

模拟数据：`192.168.1.10 GET /index.html 15824 0.043 123456`

如果一个日志文件下有多个日志格式怎么办？例如项目新版本添加一个日志字段，需要兼容旧日志匹配。

使用多模式匹配，写多个正则表达式，只要满足其中一条就能匹配成功。

```bash
# cat /opt/patterns
CID [0-9]{5,6}
EID [a-z]{3,6}

---

filter{
    grok {
        patterns_dir =>"/opt/patterns"
        match => [
            "message", "%{IP:client} %{WORD:method} %{URIPATHPARAM:request} %{NUMBER:bytes} %{NUMBER:duration} %{CID:cid}",
            "message", "%{IP:client} %{WORD:method} %{URIPATHPARAM:request} %{NUMBER:bytes} %{NUMBER:duration} %{EID:cid}"
        ]
    }
}
```

### GeoIP

## 输出插件

### file

```json
output{
    file {
        path => "/tmp/test.log"
    }
}
```

### Elasticsearch

```json
output{
    elasticsearch {
        hosts => ["192.168.2.211:9200","192.168.2.212:9200"]
        index => "microservice-product-%{+YYYY.MM.dd}"
    }
}
```