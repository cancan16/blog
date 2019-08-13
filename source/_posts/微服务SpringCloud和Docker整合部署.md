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