将本地项目上传到 GitHub 可以通过以下步骤完成：
# 在 GitHub 上创建仓库
登录 GitHub 账号，点击右上角的 "+" 号，选择 "New repository"。
输入仓库名称，选择公开 (Public) 或私有 (Private)，并根据需要添加描述和初始化文件 (README)。
点击 "Create repository" 创建仓库。
# 在本地初始化 Git 仓库
打开终端，进入本地项目的根目录。
执行 git init 命令初始化 Git 仓库。
# 添加文件到暂存区
执行 git add . 命令将所有文件添加到暂存区，或者使用 git add  添加特定文件。
#  提交更改
执行 git commit -m "Initial commit" 命令提交更改，并添加提交信息。
# 关联远程仓库
在 GitHub 上打开刚刚创建的仓库，复制仓库的 HTTPS 或 SSH 地址。
在终端执行  git remote add origin <repository_url>   命令，将本地仓库与远程仓库关联起来。
# 推送代码到 GitHub
执行 git push -u origin main 命令将本地代码推送到 GitHub 仓库的 main 分支。
# 修改分支名称
```java
git branch -m master main
```
# 删除远程分支分支
```java
git push origin --delete feature/new-feature
```
# 删除本地分支
```java
git branch -d feature/new-feature
```
# 设置HTTP代理
```java
git config --global http.proxy "http://127.0.0.1:8080"

git config --global https.proxy "http://127.0.0.1:8080"
```
