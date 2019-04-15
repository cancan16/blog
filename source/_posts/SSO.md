---
title: SSO
date: 2017-06-02 21:54:21
update: 2017-06-02 21:54:21
categories: SSO
tags: [sso]
---
### 1 单点登录

#### 1.1 单点登录特点
之前实现的登录和注册是在同一个tomcat内部完成，我们现在的系统架构是每一个系统都是由一个团队0进行维护，每个系统都是单独部署运行一个单独的tomcat，所以，不能将用户的登录信息保存到session中（多个tomcat的session是不能共享的），所以我们需要一个单独的系统来维护用户的登录信息。

普通登录：用户数据保存在session中，下次登录获取cookie的sessionId对应的值再去从session中获取用户数据。
单点登录(SSO<a href="http://blog.csdn.net/cutesource/article/details/5838693">参考</a>):
流程和普通登录类似，不同的是用户数据存放在系统外部redis缓存中，从而实现多系统数据的共享。