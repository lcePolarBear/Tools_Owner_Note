## Ansible ad-hoc 模式的使用

__命令行工具常用选项__
- -a
- -m
- -v
    - 输出更详细的执行过程信息， -vvv 可得到所有执行过程信息
- -i
    - 指定 inventory 信息，默认 /etc/ansible/hosts
- -f
    - 并发线程数，默认 5 个线程
- private-key
    - 指定密钥文件
- -u
    - 远程主机用户名
- -k
    - 远程主机用户密码
- -b
    - sudo 提权，默认是 root，使用 --become-user 指定其他用户
- -K
    - sudo 密码
- --list-hosts
    - 列出符合条件的主机列表，不执行任何其他命令
- -e
    - 设置变量 
        ```
        #ansible webservers -e a=123 -a 'echo {{ a }}'
        ```
- -B
    - 后台执行命令，超 NUM 秒后 kill 正在执行的任务
- -P
    - 定期返回后台任务进度
- -t
    - 输出信息至 DIRECTORY 目录下，结果文件以远程主机名命名
- --syntax-check
    - 语法检查 playbook 文件，不执行任何操作
- C
    - 运行检查，不执行任何操作

SSH 密码认证
- 密码也可以直接在资产清单里指定，实现免交互
    ```
    [webservers]
    10.206.240.111:22 ansible_ssh_user=root ansible_ssh_pass='123456'
    ```
- 先使用 ssh-keygen 生成密钥对，再使用 ssh-copyid 将公钥自动配置到目标主机
- 在资产清单中指定密钥
    ```
    [webservers]
    10.206.240.111:22 ansible_ssh_user=root ansible_ssh_key=/root/.ssh/id
    _rsa
    10.206.240.112:22 ansible_ssh_user=root
    ```