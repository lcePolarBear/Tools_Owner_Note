## 角色 - Roles
> Roles 是一种目录组织风格，用于层次性、结构化地组织 playbook ， roles 能够根据层次型结构自动装载变量文件、tasks 以及 handlers 等

__roles 目录结构__

__roles 基本使用__
- 角色使用
    - 使用角色的方式
        ```
        - hosts: webservers
          roles:
            - common # role 目录名称， roles 目录下一个目录代表一个 relo
            - webservers
        ```
        - 当角色以经典方式定义时，它们被视为静态导入并在 playbook 解析期间处理
    - 角色可以接受其他关键字，例如定义变量
        ```
        - hosts: webservers
          roles:
            - common
            - role: webservers
              vars:
                dir: '/opt/a'
                app_port: 5000
            - role: foo_app_instance
              vars:
                dir: '/opt/b'
                app_port: 5001
        ```
- import_role 和 include_role 
    ```
    - hosts: webservers
      tasks:
      - debug:
          msg: "before we run our role"
      - import_role:
          name: common
      - include_role:
          name: webservers
      - debug:
          msg: "after we ran our role"
    ```
- 条件判断导入角色
    ```
    - hosts: webservers
      task:
      - include_role:
          name: common
        when: "ansible_os_family == 'RedHat'"
    ```
- tags
    ```
    - hosts: webservers
      tasks:
      - import_role:
          name: webservers
        tags:
        - web
        - webserver
    ```