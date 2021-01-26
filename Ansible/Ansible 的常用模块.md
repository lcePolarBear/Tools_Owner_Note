## Ansible 的常用模块

__执行 shell 命令__
```
ansible webservers -m shell –a 'pwd' 
ansible webservers -a "/sbin/reboot"
```

__文件传输 (copy , file 和 synchronize)__
- 使用 copy 模块将文件传输到多台服务器
    ```
    ansible webservers -m copy –a "src=/etc/hosts dest=/tmp/hosts"
    ```
- 修改文件权限
    ```
    ansible webservers -m file –a "dest=/srv/foo/a.txt mode=600"
    ansible webservers -m file –a "dest=/srv/foo/b.txt mode=600 owner=mdehaan group=mdehaan"
    ```
- 在远程主机创建目录并设置权限
    ```
    ansible webservers -m file –a "dest=/path/to/c mode=755 owner=mdehaan group=mdehaan state=directory"
    ```
- 删除目录（递归）或删除文件
    ```
    ansible webservers -m file –a "dest=/path/to/c state=absent
    ```
- 增量同步网站程序
    ```
    ansible webservers -m synchronize -a "src=/data/html dest=/var/www/html"
    ```
- 排除目录
    ```
    ansible webservers -m synchronize -a "src=/root/traefik dest=/tmp rsync_opts=--exclude=.git"
    ```

__软件包管理 (yum 和 apt)__
- 确保已安装软件包，但不要更新它
    ```
    ansible webservers -m yum –a "name=vim state=present"
    ```
- 确保将软件包安装到特定版本
    ```
    ansible webservers -m yum –a "name=vim-1.5 state=present"
    ```
- 确保软件包装是最新版本
    ```
    ansible webservers -m yum –a "name=vim state=latest"
    ```
- 确保未安装包
    ```
    ansible webservers -m yum –a "name=acme state=absent"
    ```


__用户和组__
```
ansible all -m user –a "name=foo password=<crypted password here>"
ansible all -m user –a "name=foo state=absent"
```
- `name` : 创建的用户名 
- `state` : present 新增，absent 删除 
- `force` : 删除用户的时候删除家目录 
- `system` : 创建系统用户 
- `uid` : 指定 UID 
- `shell` : 指定 shell 
- `home` : 指定用户家目

__从源代码控制部署应用 (git)__
- 从 git 里拉取代码
    ```
    ansible webservers -m git –a "repo=https://git.ctnrs.com/repo.git d est=/var/www/html version=HEAD"
    ```

__定时任务管理 (cron)__
- 创建一个同步时间的计划任务，每 5 分钟同步一下服务器的时间
    ```
    ansible all –m cron –a "minute='*/5' job='ntpdate time.windows. com &>/dev/null' name='sync time'"
    ```

__管理服务__
```
ansible webservers -m -a "name=httpd state=started"
ansible webservers -m -a "name=httpd state=restarted"
ansible webservers -m -a "name=httpd state=stopped"
```

__收集系统信息 (setup)__
- 将所有主机输出信息保存到/tmp/facts 目录下，以主机名为文件名
    ```
    ansible all -m setup --tree /tmp/facts
    ```
- 过滤输出
    ```
    ansible all -m setup -a "filter=ansible_os_family"
    ansible all -m setup -a 'filter=ansible_*_mb'
    ansible all -m setup -a 'filter=ansible_eth[0-2]'
    ```
    - 常用变量
        - `ansible_all_ipv4_addresses` ：仅显示 ipv4 的信息 
        - `ansible_devices` ：仅显示磁盘设备信息 
        - `ansible_distribution` ：显示是什么系统，例 ：centos,suse 等 
        - `ansible_distribution_major_version` ：显示是系统主版本 
        - `ansible_distribution_version` ：仅显示系统版本 
        - `ansible_machine` ：显示系统类型，例` ：32 位，还是 64 位 
        - `ansible_eth0` ：仅显示 eth0 的信息 
        - `ansible_hostname` ：仅显示主机名 
        - `ansible_kernel` ：仅显示内核版本 
        - `ansible_memtotal_mb` ：显示系统总内存 
        - `ansible_memfree_mb` ：显示可用系统内存 
        - `ansible_memory_mb` ：详细显示内存情况 
        - `ansible_swaptotal_mb` ：显示总的 swap 内存 
        - `ansible_swapfree_mb` ：显示 swap 内存的可用内存 
        - `ansible_mounts` ：显示系统磁盘挂载情况 
        - `ansible_processor` ：显示 cpu 个数(具体显示每个 cpu 的型号) 
        - `ansible_processor_vcpus` ：显示 cpu 个数(只显示总的个数) 
        - `ansible_python_version` ：显示 python 版本