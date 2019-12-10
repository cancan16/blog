---
title: Spring组件及AOP基本使用操作
date: 2019-12-10 12:39:36
update: 2019-12-10 12:39:36
categories: Spring
tags: [aop]
---

<div style="text-align: center"><iframe height="230" width="260" src="https://www.ximalaya.com/thirdparty/player/sound/player.html?id=173837050&type=red" frameborder=0 allowfullscreen></iframe></div>


<img style="width: 60%;height:60%" src="https://volc1612.gitee.io/blog/images/redis%E9%AB%98%E5%B9%B6%E5%8F%91%E9%AB%98%E5%8F%AF%E7%94%A8/blackLab.jpg" />

### AOP

AOP: 面向切面编程[底层就是动态代理]
指程序在运行期间动态的将某段代码切入到指定方法位置进行运行的编程方式。

* AOP通知方式

前置通知: logStart(),在目标方法(div)运行之前运行 (@Before)
后置通知:logEnd(), 在目标方法(div)运行结束之后运行,无论正常或异常结束 (@After)
返回通知:logReturn, 在目标方法(div)正常返回之后运行 (@AfterReturning)
异常通知:logException, 在目标方法(div)出现异常后运行(@AfterThrowing)
环绕通知:动态代理, 最底层通知,手动指定执行目标方法(@Around)
