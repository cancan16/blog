---
title: 微服务必备技能Docker容器
date: 2019-08-08 21:05:16
update: 2019-08-08 21:05:16
categories: SPRINGCLOUD
tags: [Docker]
---

![微服务必备技能Docker容器-a](https://volc1612.gitee.io/blog/images/微服务必备技能Docker容器/微服务必备技能Docker容器-a.png)

### 微服务下的Docker介绍和使用场景

简介：Docker介绍和使用场景
1、什么是Dokcer
百科:一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的Linux机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口；
使用go语言编写，在LCX（linux容器）基础上进行的封装

简单来说：
    1）就是可以快速部署启动应用
    2）实现虚拟化，完整资源隔离
    3）一次编写，四处运行（有一定的限制，比如Docker是基于Linux 64bit的，无法在32bit的linux/Windows/unix环境下使用）

<!-- more -->

### 为什么要用

1、提供一次性的环境，假如需要安装Mysql，则需要安装很多依赖库、版本等，如果使用Docker则通过镜像就可以直接启动运行   

2、快速动态扩容，使用docker部署了一个应用，可以制作成镜像，然后通过Dokcer快速启动

3、组建微服务架构，可以在一个机器上模拟出多个微服务，启动多个应用

4、更好的资源隔离和共享

一句话：开箱即用，快速部署，可移植性强，环境隔离

### linux安装docker

参考：https://help.aliyun.com/document_detail/51853.html?spm=a2c4g.11186623.6.820.RaToNY

centos 7系统安装

#### 添加yum源

```bash
# yum install epel-release –y
# yum clean all
# yum list
```
#### 安装并运行Docker

```bash
# yum install docker-io –y
# systemctl start docker
```
#### 检查安装结果

```bash
# docker info
```
