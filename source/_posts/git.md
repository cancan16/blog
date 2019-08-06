---
title: git
date: 2018-08-11 23:18:58
update: 2018-10-28 23:18:58
categories: GIT
tags: [git]
---

<div align="center">
<img style="text-align: center" src="https://volc1612.gitee.io/blog/images/git/git_0.png" alt="mianshi">
</div>
<!-- more -->

### 常见错误

```
$ git status
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean

```
原因是在本地的文件夹为NewRepo，与远程仓库名称不一样

### 撤销commit

参考： https://blog.csdn.net/ligang2585116/article/details/71094887


```
Administrator@PC-201805212337 MINGW64 /e/wk/vuepro/my-project (master)
$ git reflog
bcf46eb (HEAD -> master) HEAD@{0}: commit: no message
223151b HEAD@{1}: commit: no message

Administrator@PC-201805212337 MINGW64 /e/wk/vuepro/my-project (master)
$ git show HEAD@{0}
commit bcf46eb447208863f3da6e86e88e7bd4af95cc90 (HEAD -> master)
Author: volc1612 <916215796@qq.com>
Date:   Sat Aug 11 23:46:20 2018 +0800

    no message

diff --git a/src/App.vue b/src/App.vue
index b8c89f6..cf577da 100644
--- a/src/App.vue
+++ b/src/App.vue
@@ -9,6 +9,7 @@
 // 导入组件
 import Hello from './components/Hello'
 第一次提交
+第二次提交
 export default {
   name: 'App',
   components: {

Administrator@PC-201805212337 MINGW64 /e/wk/vuepro/my-project (master)
$ git show HEAD@{1}
commit 223151bc805f8ee737aad4ab7d89b4dacfa9f3a6
Author: volc1612 <916215796@qq.com>
Date:   Sat Aug 11 23:46:04 2018 +0800

    no message

diff --git a/src/App.vue b/src/App.vue
index 56c1022..b8c89f6 100644
--- a/src/App.vue
+++ b/src/App.vue
@@ -8,6 +8,7 @@
 <script>
 // 导入组件
 import Hello from './components/Hello'
+第一次提交
 export default {
   name: 'App',
   components: {

Administrator@PC-201805212337 MINGW64 /e/wk/vuepro/my-project (master)
$ git revert HEAD@{1}
error: could not revert 223151b... no message
hint: after resolving the conflicts, mark the corrected paths
hint: with 'git add <paths>' or 'git rm <paths>'
hint: and commit the result with 'git commit'

Administrator@PC-201805212337 MINGW64 /e/wk/vuepro/my-project (master|REVERTING)
$ git push origin master
Everything up-to-date
```

### 查看所有分支

```bash
$ git branch -a
```

![git_a](https://volc1612.gitee.io/blog/images/git/git_a.png)

### 检出当前分支并创建develop本地分支

```bash
$ git checkout -b develop_one
```

### 切换分支到develop_one

```bash
$ git checkout develop_one
```

### 推送当前分支（develop_one）到远端仓库

```bash
$ git push
```

push时问题

```bash
Admin@VI-H19-PC MINGW64 /d/wk/springboothello (develop_one)
$ git push
fatal: The current branch develop_one has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin develop_one
```

具体原因： 出现这种情况主要是由于远程仓库太多，且分支较多。在默认情况下，`git push`时一般会上传到`origin`下的`master`分支上，然而当repository和branch过多，而又没有设置关联时，git就会产生疑问，因为它无法判断你的push目标。

push时指定目标分支(origin)和分支名称(develop_one)。

```bash
$ git push --set-upstream origin develop_one
Total 0 (delta 0), reused 0 (delta 0)
remote: Powered by Gitee.com
To https://gitee.com/volc1612/springboothello.git
 * [new branch]      develop_one -> develop_one
Branch 'develop_one' set up to track remote branch 'develop_one' from 'origin'.
```

### 创建feature分支和推送到远程仓库

从`master`分支检出到`feature/develop_one`新分支

```bash
Admin@VI-H19-PC MINGW64 /d/wk/springboothello (master)
$ git checkout -b feature/develop_one
Switched to a new branch 'feature/develop_one'

Admin@VI-H19-PC MINGW64 /d/wk/springboothello (feature/develop_one)
$ git push --set-upstream origin feature/develop_one
Total 0 (delta 0), reused 0 (delta 0)
remote: Powered by Gitee.com
To https://gitee.com/volc1612/springboothello.git
 * [new branch]      feature/develop_one -> feature/develop_one
Branch 'feature/develop_one' set up to track remote branch 'feature/develop_one' from 'origin'.
```

![git_b](https://volc1612.gitee.io/blog/images/git/git_b.png)

### 合并分支

使用`sourcetree`客户端合并分支，将`feature/develop_one`分支合并到`develop_one`分支中。

![git_d1](https://volc1612.gitee.io/blog/images/git/git_d1.png)

![git_e](https://volc1612.gitee.io/blog/images/git/git_e.png)

没有冲突时直接推送到developer_one分支，合并完成。

![git_c](https://volc1612.gitee.io/blog/images/git/git_c.png)


当存在冲突时，例如下面的文件冲突。可以借助`BeyondCompare`比较工具解决冲突。
首先让`sourcetree`关联`BeyondCompare`。

![git_f](https://volc1612.gitee.io/blog/images/git/git_f.png)
找到冲突的文件，打开外部工具合并。
![git_d](https://volc1612.gitee.io/blog/images/git/git_d.png)

![git_g](https://volc1612.gitee.io/blog/images/git/git_g.png)

代码：

![git_i](https://volc1612.gitee.io/blog/images/git/git_i.png)

### fetch和pull的区别

Git中从远程的分支获取最新的版本到本地有这样2个命令：

1. git fetch：相当于是从远程获取最新版本到本地，不会自动merge。

```bash
$ git fetch origin master
$ git log -p master..origin/master
$ git merge origin/master
```

以上命令的含义：
首先从远程的origin的master主分支下载最新的版本到origin/master分支上;
然后比较本地的master分支和origin/master分支的差别;
最后进行合并;

上述过程其实可以用以下更清晰的方式来进行：

```bash
$ git fetch origin master:tmp
$ git diff tmp 
$ git merge tmp
```
从远程获取最新的版本到本地的test分支上;
之后再进行比较合并;

2. git pull：相当于是从远程获取最新版本并merge到本地

```bash
git pull origin master
```
上述命令其实相当于git fetch 和 git merge，
在实际使用中，git fetch更安全一些，
因为在merge前，我们可以查看更新情况，然后再决定是否合并。

### `git pull`报错

> git pull

```
error: cannot lock ref 'refs/remotes/origin/master': Unable to create 'D:/wk/doc/.git/refs/remotes/origin/master.lock': File exists.
```
解决方法：删除`master.lock`文件

### 常见错误

```bash
[root@izbp1d9e2n6iw6mvtx07jmz ]# git pull
error: 'refs/remotes/origin/fix' exists; cannot create 'refs/remotes/origin/fix/menu-permission'
# 解决命令
[root@izbp1d9e2n6iw6mvtx07jmz ]# git update-ref -d refs/remotes/origin/fix
```

### linux更新远端分支删除远端不见的跟踪分支

查看remote地址，远程分支，还有本地分支与之相对应关系等信息

```bash
[root@izbp1d9e2n6iw6mvtx07jmz imp-server]# git remote show origin
```
在本地删除远端不见的分支

```bash
[root@izbp1d9e2n6iw6mvtx07jmz imp-server]# git remote prune origin
```