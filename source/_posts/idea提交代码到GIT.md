---
title: idea提交代码到GIT
date: 2017-12-03 15:58:38
update: 2017-12-03 15:58:38
categories: git
tags: [git,idea]
---

### idea提交代码到GIT

<!-- more -->

注意：首先在git上创建空的项目仓库

VCS->Enable Version Control...，然后选择Git
右键点击src或代码文件，Git -- Add -- Commit（先Add再Commit）
首次提交会选择提交的url，点击Define remote填写仓库url

### 使用命令提交文件夹到git
1、进入需要提交的文件的目录，右击选择git bash here
```
git init
```
2、把文件添加到版本库中，使用命令 git add .添加到暂存区里面去，不要忘记后面的小数点“.”，意为添加文件夹下的所有文件
```
git add .
```
3、用命令 git commit告诉Git，把文件提交到仓库。引号内为提交说明
```
git commit -m 'first commit'
```
4、关联到远程库
```
git remote add origin 你的远程库地址
```
如：
git remote add origin https://github.com/cade8800/ionic-demo.git
5、获取远程库与本地同步合并（如果远程库不为空必须做这一步，否则后面的提交会失败）
```
git pull --rebase origin master
```
6、把本地库的内容推送到远程，使用 git push命令，实际上是把当前分支master推送到远程。执行此命令后会要求输入用户名、密码，验证通过后即开始上传。
```
git push -u origin master
```
7、状态查询命令
```
git status
```

#### idea配置类或方法注释模板

![annotation_a](https://volc1612.gitee.io/blog/images/idea/annotation_a.png)
