## Ansible 的安装与配置

__yum 安装 ansible__
```
yum install epel-release -y
yum install ansible -y
```

__查看配置文件 /etc/ansible/ansible.cfg__
- __inventory__ `/etc/ansible/hosts`
    - 主机清单 inventory 文件，就是一些需要连接的主机
- __library__ `/usr/share/my_modules`
    - Ansible 的操作动作，无论是本地或远程，都使用一小段代码来执行，这小段代码称为模块，这个 library 参数就是指向存放 Ansible 模块的目录
- __forks__ `5`
    - 并发进程数。多少个进程同时工作，可以根据控制主机的性能和被管理节点的数量来确定
- __become__  `root`
    - 默认执行命令的用户，也可以在 playbook 中重新设置这个参数
- __remote_port__ `22`
    - 连接被管理服务器默认端口
- __host_key_checking__ `False`
    - 是否检查 SSH 主机的密钥
- __timeout__ `10`
    - SSH 连接超时时间，单位秒
- __log_path__
    - 默认不记录日志，如果想记录日志需要开启指定日志文件，需要 ansible 用户有写入日志权限
- __private_key_file__
    - 私钥 key

__主机清单 /etc/ansible/hosts__
- 主机和组
    - 未分组的主机
        ```
        green.example.com
        blue.example.com
        192.168.100.1
        192.168.100.10
        ```
    - 属于 webservers 组主机集合
        ```
        [webservers]
        alpha.example.org
        beta.example.org
        192.168.1.100
        192.168.1.110
        ```
    - 属于 dbservers 组主机集合
        ```
        [dbservers]
        db01.intranet.mydomain.net
        db02.intranet.mydomain.net
        10.25.1.56
        10.25.1.57
        ```
- 主机变量
    - 主机变量格式
        ```
        [webservers]
        host1 http_port=80 maxRequestsPerChild=808
        host2 http_port=8080 maxRequestsPerChild=909 
        ```
- 组变量
    - 变量也可以应用于整个主机组
        ```
        [webservers]
        host1
        host2

        [webservers:vars]
        ntp_server=ntp.example.com
        proxy=proxy.example.com
        ```
- 分离主机和组的变量到特定文件
    - Ansible 中的首选做法是不将变量存储在 Inventory 中，除了将变量直接存储在 Inventory 文件之外，主机和组变量还可以存储在相对于 Inventory 文件的单个文件中，采用 yaml 格式
    - vi /etc/ansible/group_vars/webservers.yml
        ```
        work_dir: /data/nginx
        ```
    - vi /etc/ansible/host_vars/192.168.1.10.yml
        ```
        nginx_port: 80
        ```
- 主机清单常用参数
    - `ansible_ssh_host` : 主机名或 IP 地址
    - `ansible_ssh_port` : SSH 端口号，默认 22
    - `ansible_ssh_user` : SSH 用户名
    - `ansible_ssh_pass` : SSH 密码
    - `ansible_ssh_private_key_file` : SSH 使用的私钥文件
    - `ansible_become` : 允许提权
    - `ansible_become_user` : 提权用户，等同于 ansible_sudo_user 或 ansible_su_user
    - `ansible_become_pass` : 提权用户密码，等同于 ansible_sudo_pass 或 ansible_su_pass