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


### `sourcetree`克隆github私有仓库

#### 生成公钥

```bash
$ ssh-keygen -t rsa -C "916215796@qq.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/Administrator/.ssh/id_rsa):
Created directory '/c/Users/Administrator/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/Administrator/.ssh/id_rsa.
Your public key has been saved in /c/Users/Administrator/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:Q1cBoScUX0I74iCrTF5iVQEtxyUN9uq9CalNSFlAb+U 916215796@qq.com
The key's randomart image is:
+---[RSA 2048]----+
|   .++*==o=o+.   |
|    .=+*.o =     |
|    oo= E *      |
|   . * = = .     |
|  + = . S        |
| = = o o .       |
|  + . = .        |
|     + . o       |
|    . . o        |
+----[SHA256]-----+
```

打开`C:\Users\Administrator\.ssh\id_rsa.pub`文件复制公钥

#### github账号添加公钥

打开登录github设置页面`SSH and GPG keys`添加`new SSH ksy`,`title`随便写，值粘贴复制的公钥，保存

#### 测试是否SSH连接是否成功

```bash
$ ssh -T git@github.com
The authenticity of host 'github.com (13.250.177.223)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,13.250.177.223' (RSA) to the list of known hosts.
Hi volc1605! You've successfully authenticated, but GitHub does not provide shell access.
```

* 报错

```sh
$ ssh -T git@github.com
Connection reset by 13.250.177.223 port 22
```
原因可能是公司网络禁用了ssh协议的22端口

* 解决

`C:\Users\Administrator\.ssh`目录下创建`config`文件，内容如下

```
Host github.com
User YourEmail@163.com
Hostname ssh.github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa
Port 443
```

* 使配置生效

`$ git config --global user.name "用户名"`
`$ git config --global user.name 配置文件中邮箱`

```sh
Administrator@PC-202001110933 MINGW64 ~/Desktop
$ git config --global user.name "volc1605"

Administrator@PC-202001110933 MINGW64 ~/Desktop
$ git config --global user.email 916215796@qq.com

Administrator@PC-202001110933 MINGW64 ~/Desktop
$ ssh -T git@github.com
The authenticity of host '[ssh.github.com]:443 ([192.30.253.122]:443)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[ssh.github.com]:443,[192.30.253.122]:443' (RSA) to the list of known hosts.
Hi volc1605! You've successfully authenticated, but GitHub does not provide shell access.
```

#### sourcetree添加SSH秘钥

选择`SSH`客户端为`OpenSSH`，`SSH`秘钥选择生成的`id_rsa`文件

![git-h](https://volc1612.gitee.io/blog/images/git/git-h.png)

#### 添加github账号

![添加账号](https://volc1612.gitee.io/blog/images/git/添加账号.png)

输入密码，这样就可以clone私有仓库了。

### sourceTree工具撤销回滚合并

![git-k](https://volc1612.gitee.io/blog/images/git/git-k.png)

### sourcetree更新代码提示错误

sourcetree提示错误error: cannot lock ref 'refs/remotes/origin/master': unable to resolve reference 'refs/remotes/origin/master': reference broken

```
$ git gc --prune=now
$ git remote prune origin
```
删除项目中origin目录

### sourcetree切换分支时报错

Please move or remove them before you switch branches

解决方法

``` sh
# 查看暂存文件
git status

# 删除
git clean -dfx
```


### 分支管理


![分支管理](https://volc1612.gitee.io/blog/images/git/分支管理.png)

**功能分支合并到release分支冲突解决**

Bob 完成了他的编码工作，同时向 release/fb 分支发起了一个 pull request。
因为发布分支已经合并了登录的代码，这时代码冲突发生了。解决冲突和合并代码是 reviewer 的责任。在这样的情况下，作为技术 Leader 就需要解决冲突和合并代码了。
现在 John 也已经完成了他的开发，同时也想把代码合并到发布分支。但 John 非常擅长于解决代码冲突。他将 release/fb 上最新的代码合并到他自己的功能分支 feature/newsfeed （通过 git pull 或 git merge 命令）。同时他解决了所有存在的冲突，现在 feature/newsfeed 已经有了所有发布分支 release/fb 的代码。
最后 John 创建了一个 pull request，由于 John 已经解决了所有问题，所以本次 pull request 不会再有冲突了。

因此通常有两种方式来解决代码冲突：

* pull request 的 reviewer 需要解决所有的代码冲突。
* 开发人员需要确保将发布分支的最新代码合并到功能分支，并且解决所有的冲突

#### git rebase

`rebase`会把你当前分支的 commit 放到公共分支的最后面,所以叫变基。就好像你从公共分支又重新拉出来这个分支一样。
举例:如果你从 master 拉了个feature分支出来,然后你提交了几个 commit,这个时候刚好有人把他开发的东西合并到 master 了,这个时候 master 就比你拉分支的时候多了几个 commit,如果这个时候你 rebase master 的话，就会把你当前的几个 commit，放到那个人 commit 的后面。

`merge`会把公共分支和你当前的commit 合并在一起，形成一个新的 commit 提交。

* 不要在公共分支使用rebase
* 本地和远端对应同一条分支,优先使用rebase,而不是merge

抛出问题:
为什么不要再公共分支使用rebase？

比如把`feature/one`分支使用`git rebase`合并到`release`分支，
因为往后放的这些`commit`都是新的,这样其他从这个公共分支拉出去的人，都需要再 rebase,相当于你 rebase 东西进来，就都是新的 commit 了。

举个例子：
1-2-3 是现在的分支状态
这个时候从原来的master ,checkout出来一个prod分支
然后master提交了4.5，prod提交了6.7
这个时候master分支状态就是1-2-3-4-5，prod状态变成1-2-3-6-7
如果在prod上用rebase master ,prod分支状态就成了1-2-3-4-5-6-7
如果是merge
1-2-3-6-7-8
........ |4-5|
会出来一个8，这个8的提交就是把4-5合进来的提交。


### sourcetree演示`merge`和`rebase`的区别

#### sourcetree客户端使用`rebase`

1. 切换到rebase-one分支；
2. 选择master分支右击，选择`将当前变更变基到master`，点解确定；
3. 解决冲突；
4. 点击拉取->继续变基，如图；
![变基操作](https://volc1612.gitee.io/blog/images/git/变基操作.png)
5. 再次点击拉取
![变基拉取](https://volc1612.gitee.io/blog/images/git/变基拉取.png)
6. 解决冲突；
7. 提交
![变基提交](https://volc1612.gitee.io/blog/images/git/变基提交.png)
8. 推送
![变基推送](https://volc1612.gitee.io/blog/images/git/变基推送.png)


场景：

* 两个功能分支`one`和`two`
* `two`分支发布了功能，并合并到了`master`分支；
* `one`分支需要从`master`分支同步`two`功能分支的代码，需要解决冲突；
* `one`分支再发布自己的功能代码，合并到`master`上；

![合并对比](https://volc1612.gitee.io/blog/images/git/合并对比.png)


merge合并merge-one到master分支后，不会保留merge-one分支合并到master分支的记录
rebase是保留的