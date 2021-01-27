## Playbook 定义变量与使用

__定义变量__
- 命令行传递
    ```
    -e VAR=VALUE
    ```
- 在 Inventory 中定义
    - 向不同的主机传递不同的变量
        ```
        IP/HOSTNAME variable_name=value
        ```
    - 向组内的所有主机传递相同的变量
        ```
        [groupname:vars]
        variable_name=value
        ```
- 在 playbook 中定义
    ```
    - hosts: webservers
      vars:
      - var_name: value
      - var_name: value
      tasks:
        - name: hello
          shell: "echo {{var_name}}"
    ```
- 在 Role 中定义
    ```
    - hosts: webservers
      roles:
        - docker
        - { role: nginx,port: 80 }
    ```
- 注册变量 `register`
    - reigster 的不单是一个值，而是一个字典
    ```
    - hsots: webservers
      tasks:
        - name: get date
          command: date + "%F_%T"
          register: date_output
        - name: echo date_output
          command: touch /tmp/{{date_output.stdout}}
    ```
- 系统信息变量 `facts`
    - 系统信息变量： facts 默认执行，可在 playbook 中直接调用
    - 属性：{{ ansible_hostname }}
    - 层级属性：{{ ansible_devices.vda.size }}
    - 数组：{{ ansible_dns.nameservers[0] }}
    - 如果用不到 facts，建议关闭，提供执行效率
    ```
    - hosts: webservers
      gather_facts: no  # 关闭 facts
      tasks:
        - name: Install nginx
          yum: name=nginx state=present
          tags: install
    ```

__访问主机变量__
- Ansible 默认提供了为主机一些变量，比较常用的有 hostvars,group_names 和 groups ，这些对于流程控制非常有用
- 例如获取当前目标主机主机名
    ```
    - hsots:webservers
      gather_facts: no
      tasks:
        - debug: msg="{{hostvars}}"
        - debug: msg="{{hostvars['192.168.1.10']['inventory_hostname']}}"
    ```
    - hostvars 返回的是一个关于主机信息的字典，可以使用字典 hostvars['变量名'] 或者 hostvars[`192.168.1.10`].inventory_hostname 方式来引用
- 获取当前主机所在的组
    ```
    - hsots:webservers
      gather_facts: no
      tasks:
        - debug: msg="{{group_names}}"
    ```
- 获取所有组（和主机）的列表
    ```
    - hsots:webservers
      gather_facts: no
      tasks:
        - debug: msg="{{groups}}"
    ```