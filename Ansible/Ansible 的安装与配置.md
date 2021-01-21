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
- 未分组的主机
    ```
    # Ex 1: Ungrouped hosts, specify before any group headers.

    ## green.example.com
    ## blue.example.com
    ## 192.168.100.1
    ## 192.168.100.10
    ```
- 属于 webservers 组主机集合
    ```
    # Ex 2: A collection of hosts belonging to the 'webservers' group

    ## [webservers]
    ## alpha.example.org
    ## beta.example.org
    ## 192.168.1.100
    ## 192.168.1.110
    ```
- 属于 dbservers 组主机集合
    ```
    # Ex 3: A collection of database servers in the 'dbservers' group

    ## [dbservers]
    ##
    ## db01.intranet.mydomain.net
    ## db02.intranet.mydomain.net
    ## 10.25.1.56
    ## 10.25.1.57
    ```
变量
- 主机变量
- 组变量
- 分离主机和组的变量到特定文件