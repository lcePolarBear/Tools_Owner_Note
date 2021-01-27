## Playbook 流程控制

__条件__
- 流程控制可以根据条件执行不同动作
    - 特定主机跳过特定步骤 
    - 特定操作系统适用软件包管理器不同 
    - 只清理磁盘空间即将满的主机

- 示例 1 ：只在目标主机 IP 是 10.202.240.112 执行该 debug 任务
    ```
    - hosts: webserver
      tasks:
        - name: Host 10.202.240.112 run this task
          debug: msg="{{ansible_default_ipv4.address}}"
          when: ansible_default_ipv4.address == '10.202.240.112'
    ```
- 示例 2 ：根据不同操作系统安装软件
    ```
    - hosts: webservers
      tasks:
        - name:Update apache version -yum
          yum: name=httpd state=present
          when: ansible_pkg_mgr == 'yum'
        - name:Update apache version -apt
          apt: name=apache2 state=present update_cache=yes
          when: ansible_pkg_mgr == 'apt'
    ```
- 示例 3 ：逻辑或：两边表达式有一方满足就执行
    ```
    tasks:
      - name: "shut down CentOS 6 systems"
        command: /sbin/shutdown -t now
        when:
          - ansible_distribution == "CentOS"
          - ansible_distribution_major_version == "6"
    ```
- 示例 4 ：逻辑运算符 and,or
    ```
    tasks:
      - name: "shut down CentOS 6 and Debian 7 systems"
        command: /sbin/shutdown -t now
        when: (ansible_distribution == "CentOS" and ansible_distribution_major_version == "6") or
              (ansible_distribution == "Debian" and ansible_distribution_ major_version == "7")
    ```

__循环__
- 遍历字典，在循环中引用子项
    ```
    - name: Add serveral users
      ansible.buildtin.usr:
        name: "{{item.name}}"
        state: present
        groups: "{{item.groups}}"
      loop:
        - { name: 'user1',groups: 'wheel'}
        - { name: 'user1',groups: 'wheel'}
    ```
- 循环和条件
    ```
    tasks:
      - command: echo {{item}}
      loop: [0,1,2,4,5]
      when: item > 5
    ```