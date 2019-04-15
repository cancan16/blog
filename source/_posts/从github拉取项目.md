---
title: 从github拉取项目
date: 2017-06-02 12:32:16
update: 2017-06-02 12:32:16
categories: GitHub
tags: [github]
---

### 1 使用github客户端windows powershell操作项目
<!-- more -->
#### 1.1 初始化本地仓库
```
PS E:\githubrepository\volc-quartz> git init
```
#### 1.2 clone项目到本地

```
PS E:\githubrepository\volc-quartz> git clone git@github.com:volc1605/volc-quartz.git
```
#### 1.3 拉取项目

```
PS E:\githubrepository\volc-quartz> git pull
fatal: No remote repository specified.  Please, specify either a URL or a
remote name from which new revisions should be fetched.
```
存在错误，解决方法，修改volc-quartz目录下的.git目录中config配置文件
内容如下

```
[core]
	bare = false
	repositoryformatversion = 0
	filemode = false
	symlinks = false
	ignorecase = true
	logallrefupdates = true
[remote "origin"]
	url = https://github.com/volc1605/volc-quartz.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master
```
url换成自己的github中volc-quartz项目的访问地址(https://github.com/volc1605/volc-quartz)，再加上.git

```
PS E:\githubrepository\volc-quartz> git pull             		-- 1.同步项目
Your configuration specifies to merge with the ref 'refs/heads/master'
from the remote, but no such ref was fetched.
PS E:\githubrepository\volc-quartz> git add --all        		-- 2.添加本地仓库(volc-quartz)改变的文件
PS E:\githubrepository\volc-quartz> git commit -m '说明备注'    -- 3.提交文件到github中的volc-quartz项目
[master (root-commit) a312267] j
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 "volc-quartz/\346\226\260\345\273\272\346\226\207\346\234\254\346\226\207\346\241\243.txt"
PS E:\githubrepository\volc-quartz> git push                    -- 4.推送到github中volc-quartz项目
Counting objects: 4, done.
```

### 2 忽略文件或文件夹提交到github(git)

#### 2.1 创建.gitignore
在项目根目录下创建.gitignore文件
进入项目跟目录
右击 Git Bash Here
执行以下命令
```
touch .gitignore
```
#### 2.2 编辑忽略文件
.gitignore

```
# 忽略test.txt文件
test.txt
# 忽略项目更目录下的test目录下的所有文件
/test/*
```



