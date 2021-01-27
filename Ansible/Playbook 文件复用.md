## Playbook 文件复用

__Ansible 有两种可重用内容的操作模式__
- include : 动态， include*  (include_tasks, include_role 等 ) ，在运行时导入
    - 可以与循环一起使用，为循环的每个项目添加任务或角色。 但—list-tags,--list-tasks 不能显示。也不能 notify 触发动态包的 handlers
- import  : 静态， import*  (import_playbook, import_tasks 等 )，在 Playbook 解析时预先导入
    - 不能与循环一起使用，将变量用于目标文件或角色名称时，不能使用 inventory,host_vars 和 group_vars 中的变量（因为是加载时导 入的），不能使用 --start-at-task 在动态包含内的任务开始执行 

__导入 yml 文件__
```
- import_playbook: webservers.yml
- import_playbook: dbservers.yml
```

__包含和导入任务文件__
- playbook
    ```
    - hosts: webservers
      gather_facts: no
      tasks:
      - include_tasks: task1.yml
      - include_tasks: task2.yml
    ```
- task1.yml
    ```
    - name: task1
      debug: msg="hello task1"
    ```
- task2.yml
    ```
    - name: task2
      debug: msg="hello task2"
    ```