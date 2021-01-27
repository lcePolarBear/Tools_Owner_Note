## jinja2 - Playbook 模板
> Jinja2 是基于 Python 实现的一种模板引擎，具有集成沙箱执行环境，Ansible 也是使用 Jinja2 动态渲染模板文件

__条件和循环__
- 示例
    - playbook
        ```
        - hosts: webservers
          vars:
            hello: 123
          tasls:
            - template: src=f.j2 dest=/tmp/f.j2
        ```
        - template 模块用于用 Jinja2 模板语言渲染生成文件，并拷贝到目标节点
    - f.j2 模板文件
        ```
        {% set list=['one','two','three'] %}

        {% for i in list %}
          {% if i =='two' %}
            -> two
          {% elif loop.index == 3 %}
            -> three
          {% else %}
            {{i}}
          {% endif %}
        {% endfor %}

        {{ hello }}
        ```
        - set 用于定义变量，jinja2 也可以使用 ansible 中定义的变量
- 遍历组中所有主机 IP
    ```
    {% for host in groups['webservers'] %}
      {{ hsotvars[host]['ansible_eth0']['ipv4']['address'] }}
    {% endfor %}
    ```
- 遍历字典
    ```
    {% set dict={'chen': '30'} %}
    {% for key,value in dict.iteritems() %}
      {{key}} -> {{value}}
    {% endfor %}
    ```

__案例：管理 Nginx 配置文件__
- hosts
    ```
    [webservers]
    192.168.1.201
    192.168.1.202
    ```
- playbook.yaml
    ```
    - hosts: webservers
      gather_facts: no
      vars:
        http_port: 80
        server_name: www.chen.com

      tasks:
        - name: Copy nginx config file
          template: src=site.conf.j2 dest=/usr/local/nginx/vhost/site.comf
          notify: reload nginx
      handlers:
        - name: reload nginx
          service: name=nginx state-reloaded
    ```
- site.conf.j2
    ```
    upstream {{server_name}} {
      {% for host in groups['webservers'] %}
        server {{ hostvars[host].inventory_hostname }}:80;
      {% endfor %}
    }
    server {
      listen        {{ http_port }};
      server_name   {{ server_name }};

      location / {
        proxy_pass http://{{server_name}};
      }
    }
    ```
- 运行
    ```
    ansible-playbook playbook.yaml
    ```
- 运行结果
    ```
    upstream www.chen.com {
      server 192.168.1.201:80;
      server 192.168.1.202:80;
    }
    server {
      listen        80;
      server_name   www.chen.com;

      location / {
        proxy_pass http://www.chen.com;
      }
    }
    ```