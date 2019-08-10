---
title: 微服务消息总线Bus结合消息队列RabbitMQ
date: 2019-08-07 07:53:15
update: 2019-08-07 07:53:15
categories: SPRINGCLOUD
tags: [Bus]
---

### 消息总线Bus介绍和使用场景

1、什么是消息
一个事件，需要广播或者单独传递给某个接口

2、为什么使用这个
配置更新了，但是其他系统不知道是否更新
		
3. RabbitMQ介绍	
官方文档：http://www.rabbitmq.com/getstarted.html
中文文档：http://rabbitmq.mr-ping.com/

<!-- more -->

![微服务消息总线Bus结合消息队列RabbitMQ-a](https://volc1612.gitee.io/blog/images/微服务消息总线Bus结合消息队列RabbitMQ/微服务消息总线Bus结合消息队列RabbitMQ-a.png)

### 使用docker安装rabbitmq

安装步骤
1)拉取镜像

```bash
# docker pull rabbitmq:management
```
2)查看当前镜像列表

```bash
# docker images
```
3)删除指定镜像

```bash
# 删除指定镜像
# docker rmi IMAGE_ID
# 删除指定容器
# docker rm IMAGE_ID
# 强制删除指定镜像
# docker rmi -f IMAGE_ID
```

4)创建容器

```bash
[root@localhost ~]# docker run -d --name="myrabbitmq" -p 5671:5671 -p 15672:15672 rabbitmq:management
5b5fa9406cd016587601dc074a00f16dfcf4e5905a0bcd3c2c1f1e64d2608e76
```

参数讲解: 
run: 创建一个新的容器并运行一个命令
-d: 后台运行容器，并返回容器ID
-p: 端口映射，格式为：主机(宿主)端口:容器端口
--name="rabbitmq": 为容器指定一个名称

5)查看当前运行在docker容器中程序

```bash
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                                                        NAMES
5b5fa9406cd0        rabbitmq:management   "docker-entrypoint..."   47 seconds ago      Up 46 seconds       4369/tcp, 5672/tcp, 0.0.0.0:5671->5671/tcp, 15671/tcp, 25672/tcp, 0.0.0.0:15672->15672/tcp   myrabbitmq
```

查看VMware虚拟机里的Centos7的IP

```bash
# ip addr
...
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:a0:25:58 brd ff:ff:ff:ff:ff:ff
    inet 192.168.2.113/24 brd 192.168.2.255 scope global noprefixroute dynamic ens33
       valid_lft 5687sec preferred_lft 5687sec
    inet6 fe80::506:fd13:3d02:50ba/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
...
```
192.168.2.113就是对外(局域网)提供的ip

