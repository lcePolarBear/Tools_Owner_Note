## Git 命令指南
* 安装 Git
```
yum install git
```
* 配置 Git 的使用者信息
```
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```
* 克隆 Git 仓库
```
git clone https://github.com/b3log/solo.git
```
* 建立 Git 仓库
```
git init
```
* Git 基础指令
    * 查看更改内容
    ```
    git diff
    ```
    * 添加修改文件
    ```
    git add file.txt
    ```
    * 删除文件
    ```
    git rm file.txt
    ```
    * 查看当前仓库状态
    ```
    git status
    ```
    * 提交修改文件
    ```
    git commit -m "commit show"
    ```
* 版本控制
    * 显示从最近到最远的提交日志<br>
    显示提交日志的 commit id
    ```
    git log
    git log --pretty=oneline
    ```
    * 版本回滚
    ```
    git reset --hard commit_id
    ```
    对未 add 操作的回滚
    ```
    git checkout -- file.txt
    ```
    对已 add 操作的回滚
    ```
    git reset HEAD file.txt
    ```
