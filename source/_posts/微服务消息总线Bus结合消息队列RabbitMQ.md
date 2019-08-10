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
[root@localhost ~]# docker run -d --name="myrabbitmq" -p 5672:5672 -p 15672:15672 rabbitmq:management
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

浏览器访问： http://192.168.2.113:15672登录rabbitmq

### config-client实时更新配置文件

如：product-server服务

#### config-client服务添加依赖

```xml
<!--配置中心结合消息队列-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
```

#### 暴露端点

```xml
#暴露全部的监控信息
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

#### 配置RabbitMQ的连接

```xml
spring:
    rabbitmq:
        host: localhost
        port: 5672
        username: guest
        password: guest
```

#### 需要刷新配置的地方，增加注解类注解@RefreshScope

```java
@RefreshScope
public class ProductController {}
```

#### 触发回调

当配置中心仓库product-server项目的配置文件发生变化时，触发product-server项目回调方法

post请求：http://localhost:8771/actuator/bus-refresh

![微服务消息总线Bus结合消息队列RabbitMQ-b](https://volc1612.gitee.io/blog/images/微服务消息总线Bus结合消息队列RabbitMQ/微服务消息总线Bus结合消息队列RabbitMQ-b.png)

#### 验证配置文件是否实时更新

访问`product-server`接口获取配置文件中env配置值
`http://localhost:8771/getConfigByServer`

#### config-client集群部署，配置文件会实时获取

触发回调任意的`config-clinet`集群中的一个节点，其他节点也能实时更新配置文件

**动态刷新配置: 在开发和测试环境使用，尽量少在生产环境使用，生产环境尽量重新启动配置中心服务**