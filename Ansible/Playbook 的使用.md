## Playbook 的使用

__自动部署 nginx__
- 创建 `nginx.yml` 文件
    ```
    - hosts: webservers
      vars:
        hello: Ansible
      tasks:
      - name: Add repo
        yum_repository:
          name: nginx
          description: nginx repo
          baseurl: http://nginx.org/packages/centos/7/$basearch/
          gpgcheck: no
          enable: 1
      - name: Install nginx
        yum: 
          name: nginx
          state: latest
      - name: Copy nginx configuration file
        copy:
          src: ./site.conf
          dest: /etc/nginx/conf.d/site.conf
      - name: Start nginx
        service:
          name:nginx
          state: started
      - name: Create wwwroot directory
        file:
          dest: /var/www/html
          state: directory
      - name: Create test page index.html
        shell: echo "hello {{hello}}" > /var/www/html/index.html
    ```
- 创建 nginx 配置文件 `site.conf`
    ```
    server {
      listen 81;
      server_name localhost;
      location / {
        root /var/www/html;
        index index.html;
      }
    }
    ```
- 执行 playbook
    ```
    absible-playbook nginx.yml
    ```

__Playbook 文件结构__
- 一个 playbook 文件也可以写多个 play
    ```
    - name: play1
      hosts: webservers
      remote_user: webuser
      vars:
        var_name: value
      tasks:
        - name: echo
          shell: "echo {{var_name}}"

    - name: play2
      hosts: db
      remote_user: dbuser
      vars:
        var_name: value
      tasks:
        - name: echo
          shell: "echo {{var_name}}"
    ```
- 主机和用户
    - sudo 提权执行
        ```
        - hosts: webservers
          remote_user: chen
          become: yes
        ```
    - 针对特定任务
        ```
        - hosts: webservices
          tasks:
            - service:
                name: nginx
                state: started
              remote_user:chen
              become: yes
        ```
- 任务
    - 启动服务
        ```
        tasks:
          - name: make sure apache is runing
            service:
              name: httpd
              state: started
        ```
    - command 和 shell 模块是只取参数列表，并没有使用 key=value 形式
        ```
        tasks:
          - name: enable selinux
            command: /sbin/setenforce 1
        ```
        - command 和 shell 模块关心的是返回状态码，如果返回非 0 说明执行失败（中断 执行），但可能有些命令成功退出状态码不为 0 ，但也不希望中断执行，可以这样做
            ```
            tasks:
              - name: run this command and ignore the result
                shell: /usr/bin/somecommand || /bin/true
            ```
- 变量
    - 变量引用示例
        ```
        - hosts: webservers
          vars:
            vhost: test
          remote_user: younaem

          tasks:
          - name: create a virtual host file for {{ vhost }}
            template:
              src: somefile.j2
              dest: /etc/httpd/conf.d/{{ vhost }}
        ```
- 语法检查与调试
    - 检查 yml 语法
        ```
        ansible-playbook main.yml --syntax-check
        ```
    - 使用 debug 打印执行期间的语句，用于调试
        ```
        - hosts: webservers
          tasks:
            - debug: msg="{{group_names}}"
            - debug: msg="{{inventory_hostname}}"
            - debug: msg="{{ansible_hostname}}"
        ```

__在变更时运行特定任务 (handlers)__
- Playbooks 具备一个可用于响应变化的基本事件系统，可以基于某个任务结束后触发另一个任务
- 示例：当拷贝的配置文件有改动时才触发重启
    ```
    - hosts: webservers
      tasks:
      - name: write the nginx config file
        copy:
          src: ./site.conf
          dest: /etc/nginx/conf.d/site.conf
        notify:
          - restart nginx
      - name: ensure nginx is runing
        service:
          name: nginx
          state: started
      handlers:
        - name: restart nginx
          service: name=nginx state=restarted
    ```
    - `notify` : 变更通知，指定 handlers
    - `handlers` : 处理程序，由特定条件触发的 Tasks
- handlers 只会在 playbook 执行的末尾运行一次，如果你想立即执行处理程序，可增加 `meta` 字段声明
    ```
    - hosts: webservers
      tasks:
      - name: write the nginx config file
        copy:
          src: ./site.conf
          dest: /etc/nginx/conf.d/site.conf
        notify:
          - restart nginx
      - meta: flush_handlers
      - name: ensure nginx is runing
        service:
          name: nginx
          state: started
      handlers:
        - name: restart nginx
          service: name=nginx state=restarted
    ```

__任务控制 (tags)__
- 通过给任务打标签 `tags` 的方式可以实现只运行整个剧本的特定部分
    ```
    - hosts: webservers
      gather_facts: no
      tasks:
        - name: Install nginx
          yum: name=nginx state=present
          tags: install

        - name: Copy nginx configuration file
          copy: src=nginx.conf dest=/etc/nginx/nginx.conf
          tags: config
    ```
- 指定执行某个任务
    ```
    ansible-playbook nginx.yml --tags "config,install"
    ```
- 跳过某一个任务
    ```
    ansible-playbook nginx.yml --skip-tags "install"
    ```

__自动部署 Tomcat__
```
- hosts: webservers
  vars:
    tomcat_version: 8.5.34
    tomcat_install_dir: /usr/lcoal
  
  tasks:
    - name: Install jdk1.8
      yum: name=java-1.8.0-openjdk state=present

    - name: Download tomcat
      get_url: url=http://mirrors.hust.edu.cn/apache/tomcat/tomcat-8/v{{ tomcat_version }}/bin/apache-tomcat-{{ tomcat_version }}.tar.gz dest=/tmp

    - name: Unarchive tomcat.tar.gz
      unarchive:
        src: /tmp/apache-tomcat-{{ tomcat_version }}.tar.gz
        dest: "{{ tomcat_install_dir }}"
        copy: no 
        # no 表示 copy 过程只执行在目标端， yes 表示将本地的文件解压并上传
    - name: start tomcat
      shell: cd {{ tomcat_install_dir }} &&
             mv apache-tomcat-{{ tomcat_version }} tomcat8 |true &&
             cd tomcat8/bin && nohup ./startup.sh &

```