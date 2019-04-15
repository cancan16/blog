---
title: 使用idea开发工具同步本地代码到github远程仓库
date: 2017-03-28 17:42:33
update: 2017-03-28 17:42:33
categories: GIT
tags: [git]
---
![taiji](http://p1.pstatp.com/large/19420003a59e4ef41c50)
<!-- more -->
### 1 使用idea开发工具同步本地代码到github远程仓库
git clone
git fetch # 抓取远程仓库中更新的数据
git merge # 合并将本地更改与其他的更改。通常，合并远程更新的数据和本地修改的数据
git pull

#### 1.1 本地配置远程提交仓库
##### 1）idea关联(初始化)本地仓库
本地git仓库地址E:\projectidea\springwind(也就是idea中项目所在地址)。
##### 2）本地提交
选择项目中的src和pom右击git，先add提交的内容，再点击commit directories，最后只选择commit提交。
##### 3）关联远程仓库
在远程创建没有README.md的仓库，如果创建含有文件的远程仓库，在本地关联时会遇到问题，下面介绍了解决的方法。
这里为了方便在远程创建了空的仓库，github上的项目仓库url: https://github.com/volc1605/springwindide.git

```
# 进入本地项目目录，在第1步idea已经关联了这个目录
PS E:\> cd .\projectidea
PS E:\projectidea> cd .\springwind
# 关联远程仓库地址
PS E:\projectidea\springwind> git remote add origin https://github.com/volc1605/springwindide.git
# 下面一行命令意思是本地的master分支推送到origin主机，同时指定origin为默认主机，后面就可以不加任何参数使用git push了
PS E:\projectidea\springwind> git push -u origin master
To https://github.com/volc1605/springwindide.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'https://github.com/volc1605/springwindide.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
PS E:\projectidea\springwind> git pull --rebase origin master
remote: Counting objects: 5, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 5 (delta 0), reused 3 (delta 0), pack-reused 0
warning: no common commits
Unpacking objects: 100% (5/5), done.
From https://github.com/volc1605/springwindide
 * branch            master     -> FETCH_HEAD
 * [new branch]      master     -> origin/master
First, rewinding head to replay your work on top of it...
Applying: giiigiigi  # 第2步本地提交的注释信息
PS E:\projectidea\springwind>
```
这时恭喜你！本地项目已和github远程同步成功。

#### 1.2 遇到的问题
```
********************远程仓库无文件时***************
PS E:\test> git remote add origin https://github.com/volc1605/test.git
PS E:\test> git push -u origin master
error: src refspec master does not match any.
error: failed to push some refs to 'https://github.com/volc1605/test.git'
# 产生的原因是本地没有文件提交
# 解决方法：
# 在本地仓库随意新建一个文件，linux下执行**touch README**命令即可
PS E:\test> git add --all
PS E:\test> git commit -m 'first commit'
[master (root-commit) 4fae4c6] first commit
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 "\346\226\260\345\273\272\346\226\207\346\234\254\346\226\207\346\241\243.txt"
PS E:\test> git push origin master
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 218 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/volc1605/test.git
 * [new branch]      master -> master
PS E:\test>

********************远程仓库有文件时***************
PS E:\test> cd ../test1
PS E:\test1> git remote add origin https://github.com/volc1605/test.git
PS E:\test1> git push origin master
error: src refspec master does not match any.
error: failed to push some refs to 'https://github.com/volc1605/test.git'
PS E:\test1> git push origin master
error: src refspec master does not match any.
error: failed to push some refs to 'https://github.com/volc1605/test.git'
PS E:\test1> git add --all
PS E:\test1> git commit -m 'test'
[master (root-commit) a52ce82] test
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 ee.txt
PS E:\test1> git push origin master
To https://github.com/volc1605/test.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'https://github.com/volc1605/test.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
# 产生的原因是本地和远程不同步，无法建立连接，提交拒绝
# 解决方法是先同步远程代码，再提交
PS E:\test1> git pull --rebase origin master
remote: Counting objects: 5, done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 5 (delta 0), reused 5 (delta 0), pack-reused 0
warning: no common commits
Unpacking objects: 100% (5/5), done.
From https://github.com/volc1605/test
 * branch            master     -> FETCH_HEAD
 * [new branch]      master     -> origin/master
First, rewinding head to replay your work on top of it...
Applying: test
# 这时远程的代码已经同步到本地了，刚才本地修改的代码已经commit，这时再push到远程就没有问题了。
PS E:\test1> git push origin master
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 215 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local objects.
To https://github.com/volc1605/test.git
   534a267..f6fed00  master -> master
PS E:\test1>
```

### 2 简单方法同步
intelij中 VCS->Checkout from Version Contrl—>Git中粘贴远程仓库url地址
本地修改代码后提交到远程仓库会提示输入github的账号密码。