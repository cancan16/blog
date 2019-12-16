---
title: rebase和merge
date: 2019-12-16 23:18:58
update: 2019-12-16 23:18:58
categories: GIT
tags: [git]
---

<img style="width: 80%;height:80%" src="https://volc1612.gitee.io/blog/images/rebase和merge/风景.jpg"/>

<!-- more -->

### 两个区别

场景：
* 功能分支从`master`分支检出作为新的功能分支；
* `master`分支提交两次，功能分支提交两次；
* 功能分支需要更新`master`分支上的提交；

#### merge功能分支

merge合并前分支情况

![merge合并前](https://volc1612.gitee.io/blog/images/rebase和merge/merge合并前分支.png)

merge合并后分支情况

![merge合并后](https://volc1612.gitee.io/blog/images/rebase和merge/merge合并后.png)

* `feature/merge`分支提交ID过程

```
c6e30e4
2396f24
491cff9
8f4b704
```

`8f4b704`是两个分支共同起点，`c6e30e4`这次提交，合并了`master`分支的`d60feae`和`f2f4577`；

#### rebase功能分支

rebase合并前分支情况

![rebase合并前分支](https://volc1612.gitee.io/blog/images/rebase和merge/rebase合并前分支.png)

rebase合并后分支情况

![rebase合并后](https://volc1612.gitee.io/blog/images/rebase和merge/rebase合并后.png)

* `feature/rebase`分支提交ID过程

```
d41b3e2
bc73c93
20d849a
38fcda5
ff27b1d
f2f4577
```

`f2f4577`是两个分支的共同起点，`rebase`命令把`master`变为基础，先把`master`上提交的`ff27b1d`和`38fcda5`并入`rebase`分支，再依次提交自己分支的`782a1ec`和`de84fff`，由于合并代码冲突从新提交，导致生成了新的ID值分别是`20d849a`和`bc73c93`，最后提交推送生成了一个空记录`d41b3e2`。由此可见`rebase`操作不会生成新的提交，而是将两个分支融合成一个线性的提交。

**总结**

当需要保留详细的合并信息的时候建议使用git merge，特别是需要将分支合并进入master分支时；当发现自己修改某个功能时，频繁进行了git commit提交时，发现其实过多的提交信息没有必要时，可以尝试git rebase



