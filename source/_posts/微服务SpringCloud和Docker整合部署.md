---
title: 微服务SpringCloud和Docker整合部署
date: 2019-08-11 10:58:09
update: 2019-08-11 10:58:09
categories: SPRINGCLOUD
tags: [Docker]
---

### 构建SpringBoot应用docker镜像

<!-- more -->

#### maven里面添加配置pom.xml
		
```xml
<properties>
    <docker.image.prefix>xdclass</docker.image.prefix>
</properties>
<build>
    <finalName>docker-demo</finalName>
    <plugins>
        <plugin>
            <groupId>com.spotify</groupId>
            <artifactId>dockerfile-maven-plugin</artifactId>
            <version>1.3.6</version>
            <configuration>
                <repository>${docker.image.prefix}/${project.artifactId}</repository>
                <buildArgs>
                    <JAR_FILE>target/${project.build.finalName}.jar</JAR_FILE>
                </buildArgs>
            </configuration>
        </plugin>
    </plugins>
</build>
```

配置讲解
Spotify的`docker-maven-plugin`插件是用maven插件方式构建docker镜像的。
${project.build.finalName} 产出物名称，缺省为${project.artifactId}-${project.version}

### springboot打包镜像

#### 创建Dockerfile

在项目根目录创建`Dockerfile`文件（可以修改为src/main/docker/Dockerfile,如果修则需要制定路径）内容如下
什么是Dockerfile : 由一系列命令和参数构成的脚本，这些命令应用于基础镜像, 最终创建一个新的镜像。

```
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ARG JAR_FILE
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

参数讲解：
`FROM`: <image>:<tag> 需要一个基础镜像，可以是公共的或者是私有的， 后续构建会基于此镜像，如果同一个Dockerfile中建立多个镜像时，可以使用多个FROM指令
`VOLUME`: 配置一个具有持久化功能的目录，主机 /var/lib/docker 目录下创建了一个临时文件，并链接到容器的/tmp。改步骤是可选的，如果涉及到文件系统的应用就很有必要了。/tmp目录用来持久化到 Docker 数据文件夹，因为 Spring Boot 使用的内嵌 Tomcat 容器默认使用/tmp作为工作目录 
`ARG`: 设置编译镜像时加入的参数， ENV 是设置容器的环境变量
`COPY`: 只支持将本地文件复制到容器 ,还有个ADD更强大但复杂点
`ENTRYPOINT`: 容器启动时执行的命令
`EXPOSE`: 8080 暴露镜像端口

#### 本地构建镜像

使用本地的docker构建本地项目

##### IDEA终端控制台执行使用`MAVEN`构建`springboot`项目镜像

win环境下的docker需要开启2375口号

![微服务SpringCloud和Docker整合部署-a](https://volc1612.gitee.io/blog/images/微服务SpringCloud和Docker整合部署/微服务SpringCloud和Docker整合部署-a.png)


```bash
$ mvn install dockerfile:build
```

##### 本地win环境打包报错

报错内容
```
WARNING: HK2 service reification failed for [org.glassfish.jersey.message.internal.DataSourceProvider]...
```
原因是`dockerfile-maven-plugin`打包插件需要依赖`activation`，修改不配置插件如下

```xml
<build>
    <finalName>eureka-server</finalName>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
        <plugin>
            <groupId>com.spotify</groupId>
            <artifactId>dockerfile-maven-plugin</artifactId>
            <version>1.3.6</version>
            <configuration>
                <repository>${docker.image.prefix}/${project.artifactId}</repository>
                <buildArgs>
                    <JAR_FILE>target/${project.build.finalName}.jar</JAR_FILE>
                </buildArgs>
            </configuration>
            <dependencies>
                <dependency>
                    <groupId>javax.activation</groupId>
                    <artifactId>activation</artifactId>
                    <version>1.1.1</version>
                </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>
```

构建完成后`docker images`查看镜像列表，已经打包完成

### 本地推送到阿里云私有镜像Registry

```bash
$ sudo docker login --username=cs12337 registry.cn-hangzhou.aliyuncs.com
$ sudo docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/my-private-rmi/volc:[镜像版本号]
$ sudo docker push registry.cn-hangzhou.aliyuncs.com/my-private-rmi/volc:[镜像版本号]
```

[ImageId]: 本地打包好的项目镜像ID
[镜像版本号]: 自己指定镜像版本号

### 公网服务器从阿里云私有镜像Registry拉取镜像并运行项目

```bash
$ sudo docker login --username=cs12337 registry.cn-hangzhou.aliyuncs.com
$ sudo docker pull registry.cn-hangzhou.aliyuncs.com/my-private-rmi/volc:[镜像版本号]
```

拉取成功后`docker images`查看项目镜像

项目部署到容器中，并启动该容器
`$ docker run -d --name [容器名称] -p [对外端口号]:[docker容器映射该项目的端口号]  [镜像ID]`
```bash
$ docker run -d --name docker_demo -p 8099:8080  a1b9fc71720d
```

### 查看项目启动日志

`$ docker logs -f  [容器ID]`
```bash
$ docker logs -f  containerid
```

### 本地打包遇到的问题

打包配置中心服务时，会检测注册到eureka的地址是否正确，配置中心服务配置文件中注册中心的地址应该填写内网地址。

```
com.sun.jersey.api.client.ClientHandlerException: java.net.ConnectException: Connection refused: connect
```

#### 打包跳过单元测试

注意：
1、maven打包构建，会触发单元测试，部分情况可以跳过，
mvn install -Dmaven.test.skip=true dockerfile:build


### docker安装部署redis

1、搜索镜像 docker search redis

2、拉取 docker pull docker.io/redis

3、启动 docker run --name "my_redis" -p 6379:6379 -d 4e8db158f18d
参考：
docker run --name "xd_redis" -p 6379:6379 -d 4e8db158f18d --requirepass "123456" -v $PWD/data:/data

### docker登录redis

`docker exec -it [容器ID] redis-cli`

```
$ docker exec -it 36b947a3de0b redis-cli
```

### 服务注册到eureka中，ip有误的解决方法

![微服务SpringCloud和Docker整合部署-b](https://volc1612.gitee.io/blog/images/微服务SpringCloud和Docker整合部署/微服务SpringCloud和Docker整合部署-b.png)

应该显示IP或域名

解决方式：

```yml
eureka:
  client:
    serviceUrl:
      defaultZone: http://10.0.75.1:8761/eureka/
  instance:
    instance-id: ${spring.cloud.client.ip-address}:${server.port}
    prefer-ip-address: true
```

重新注册到注册中心

![微服务SpringCloud和Docker整合部署-c](https://volc1612.gitee.io/blog/images/微服务SpringCloud和Docker整合部署/微服务SpringCloud和Docker整合部署-c.png)

### docker部署网关服务启动日志报错

由于连接`rabbitmq`配置地址不是内网ip，网关服务一直尝试连接`rabbitmq`

```
ramework.amqp.AmqpConnectException: java.net.ConnectException: Connection refused (Connection refused)
2019-08-17 06:20:09.409  INFO [product-service,,,] 1 --- [WjeCqDzVgEkQ-10] o.s.a.r.l.SimpleMessageListenerContainer : Restarting Consumer@598d4c54: tags=[{}], channel=null, acknowledgeMode=AUTO local queue size=0
2019-08-17 06:20:09.412  INFO [product-service,,,] 1 --- [WjeCqDzVgEkQ-11] o.s.a.r.c.CachingConnectionFactory       : Attempting to connect to: [127.0.0.1:5672]
```

解决方法修改配置文件连接`rabbitmq`改为内网ip，重新启动容器

```yml
#服务的名称
spring:
  rabbitmq:
    host: 10.0.75.1
    port: 5672
    username: guest
    password: guest
```


**生产环境不能用`localhost`或者`127.0.0.1`，最好使用内网IP**