# Kibana 可视化

```yaml
# sed -e '/^#/d' config/kibana.yml | more

server.port: 5601
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://192.168.2.211:9200"]
i18n.locale: "zh-CN"
```

```bash
# cat /usr/lib/systemd/system/kibana.service

[Unit]
Description=kibana

[Service]
ExecStart=/opt/kibana/bin/kibana --allow-root
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

![https://docimg1.docs.qq.com/image/egkBTmspTXAGgQkN61gSuQ.png?w=1280&h=495.3333333333333](https://docimg1.docs.qq.com/image/egkBTmspTXAGgQkN61gSuQ.png?w=1280&h=495.3333333333333)

![https://docimg4.docs.qq.com/image/dtnjq5Zg4xQ0PdNWG9oGag.png?w=1280&h=489.3333333333333](https://docimg4.docs.qq.com/image/dtnjq5Zg4xQ0PdNWG9oGag.png?w=1280&h=489.3333333333333)

![https://docimg8.docs.qq.com/image/obs838VKdpkwcLijQxCozg.png?w=1280&h=500.6666666666667](https://docimg8.docs.qq.com/image/obs838VKdpkwcLijQxCozg.png?w=1280&h=500.6666666666667)