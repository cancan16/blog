---
title: 微服务必备技能Docker容器
date: 2019-08-08 21:05:16
update: 2019-08-08 21:05:16
categories: SPRINGCLOUD
tags: [Docker]
---

![微服务必备技能Docker容器-a](https://volc1612.gitee.io/blog/images/微服务必备技能Docker容器/微服务必备技能Docker容器-a.png)

### 概念

* Docker 镜像 - Docker images：

容器运行时的只读模板，操作系统+软件运行环境+用户程序
class User{
    private String userName;
    private int age;
}

* Docker 容器 - Docker containers：

容器包含了某个应用运行所需要的全部环境
User user = new User()


* Docker 仓库 - Docker registeries： 

<a href="https://hub.docker.com/">docker镜像仓库</a>

用来保存镜像，有公有和私有仓库，好比Maven的中央仓库和本地私服
镜像仓库：	

（参考）配置国内镜像仓库：https://blog.csdn.net/zzy1078689276/article/details/77371782

对比面向对象的方式
Dokcer 里面的镜像 : Java里面的类Class
Docker 里面的容器 : Java里面的对象Object
通过类创建对象，通过镜像创建容器

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

### docker常用命令

#### 搜索镜像`docker search xxx`

```bash
[root@localhost volc]# docker search rabbitmq:manage
INDEX       NAME                                          DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
docker.io   docker.io/macintoshplus/rabbitmq-management   Based on rabbitmq:management whit python a...   5                    [OK]
docker.io   docker.io/xiaochunping/rabbitmq               xiaochunping/rabbitmq:management   2018-06-30   3                    
docker.io   docker.io/transmitsms/rabbitmq-sharded        Fork of rabbitmq:management with sharded_e...   0                    
```

#### 列出当前系统存在的镜像`docker images`

```bash
[root@localhost volc]# docker images
REPOSITORY           TAG                 IMAGE ID            CREATED             SIZE
docker.io/rabbitmq   management          99cbda713eff        4 days ago          177 MB
```
#### 拉去镜像`docker pull xxx`

docker pull xxx
xxx是具体某个镜像名称(格式 REPOSITORY:TAG)
REPOSITORY：表示镜像的仓库源,TAG：镜像的标签

#### 删除镜像`docker rmi IMAGE_ID`
#### 强制删除镜像`docker rmi -f IMAGE_ID`

#### 设置开机启动docker

```bash
[root@localhost volc]# systemctl enable docker.service
```

#### 运行一个容器

运行一个容器：docker run -d --name "myrabbitmq" -p 5672:5672 -p 15672:15672 rabbitmq:management

```bash
# docker run -d --name "myrabbitmq" -p 5672:5672 -p 15672:15672 rabbitmq:management
```
docker run - 运行一个容器
-d 后台运行
-p 端口映射
rabbitmq:management  (格式 REPOSITORY:TAG)，如果不指定tag，默认使用最新的
--name "xxx"

### 运行一个已存在的容器

```bash
# docker start 容器ID
```

### 停止一个运行的容器

```bash
# docker stop 容器ID
```

#### 查看当前运行的容器

```bash
# docker ps
```

#### 检查容器内部信息`docker inspect 容器ID`

```bash
# docker inspect 容器ID
```

#### 列出已存在所有容器

```bash
# docker ps -as
查看全部container，包括exited的容器，并列出容器的大小。
```

### 使用docker部署Nginx

#### 获取镜像 

`docker run`(首先会从本地找镜像，如果有则直接启动，没有的话，从镜像仓库拉起，再启动)

```bash
# docker search nignx
```

#### 列举查看本地是否有镜像

```bash
# docker images
```
#### 拉取镜像

```bash
# docker pull nignx
```

#### 启动

```bash
# docker run -d --name "my_nginx" -p 8088:80 nginx
# docker run -d --name "my_nginx2" -p 8089:80 nginx
```

#### 访问

本地访问

```bash
[root@localhost volc]# curl http://localhost:8088
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

如果是阿里云服务，记得配置安全组，腾讯云也需要配置，这个就是一个防火墙

### 搭建私有docker镜像仓库

#### 创建阿里云私有镜像仓库

登录阿里云访问阿里云镜像仓库
阿里云镜像仓库：https://cr.console.aliyun.com/cn-hangzhou/instances/repositories
点击管理控制台-》初次使用会提示开通，然后设置密码

创建本地仓库
![微服务必备技能Docker容器-b](https://volc1612.gitee.io/blog/images/微服务必备技能Docker容器/微服务必备技能Docker容器-b.png)

#### 使用私有镜像仓库

* 登录阿里云Docker Registry

```bash
$ sudo docker login --username=*** registry.cn-hangzhou.aliyuncs.com
```

* 将镜像推送到Registry

打包

```bash
$ sudo docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/my-private-rmi/volc:[镜像版本号]
```

例如： `$ sudo docker tag 99cbda713eff registry.cn-hangzhou.aliyuncs.com/my-private-rmi/volc:my-rabbitmq`

推送

```bash
$ sudo docker push registry.cn-hangzhou.aliyuncs.com/my-private-rmi/volc:[镜像版本号]
```

例如： `$ sudo docker push registry.cn-hangzhou.aliyuncs.com/my-private-rmi/volc:my-rabbitmq`

![微服务必备技能Docker容器-c](https://volc1612.gitee.io/blog/images/微服务必备技能Docker容器/微服务必备技能Docker容器-c.png)


* 从Registry中拉取镜像

登录成功后拉取镜像

```bash
$ sudo docker pull registry.cn-hangzhou.aliyuncs.com/my-private-rmi/volc:[镜像版本号]
```

例如： `sudo docker pull registry.cn-hangzhou.aliyuncs.com/my-private-rmi/volc:my-rabbitmq`

* 私有镜像放入容器并启动

```bash
[root@localhost volc]# docker images
REPOSITORY                                              TAG                 IMAGE ID            CREATED             SIZE
docker.io/rabbitmq                                      management          99cbda713eff        5 days ago          177 MB
registry.cn-hangzhou.aliyuncs.com/my-private-rmi/volc   my-rabbitmq         99cbda713eff        5 days ago          177 MB
docker.io/nginx                                         latest              e445ab08b2be        2 weeks ago         126 MB
```

* 启动容器

```bash
$ docker run -d --name [容器名称] -p 5672:5672 -p 15672:15672 [私有镜像ID]
```

例如：

```bash
[root@localhost volc]# docker run -d --name "my-rabbitmq" -p 5672:5672 -p 15672:15672 99cbda713eff
8eb8612473f15d78b58016899340e10a2dd645c7bcbef5369bac26e9c5ead21c
[root@localhost volc]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                                                                        NAMES
8eb8612473f1        99cbda713eff        "docker-entrypoint..."   6 seconds ago       Up 5 seconds        4369/tcp, 5671/tcp, 0.0.0.0:5672->5672/tcp, 15671/tcp, 25672/tcp, 0.0.0.0:15672->15672/tcp   my-rabbitmq
e785e915a1b4        nginx               "nginx -g 'daemon ..."   About an hour ago   Up About an hour    0.0.0.0:8088->80/tcp                                                                         my_nginx
```