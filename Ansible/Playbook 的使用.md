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

Playbook 文件结构
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