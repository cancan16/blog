---
title: Maven
date: 2016-11-26 11:38:25
update: 2016-11-26 11:38:25
categories: Maven
tags: [maven]
---

# Maven项目不显示 Maven Dependencies

从别目录结构正确的项目中复制.classpath文件，覆盖不显示Maven Dependencies的项目目录中的.classpath

<!-- more -->

# 去掉dynamic web module勾选后，每次更新maven项目都会出现以下结构

![maven](https://volc1612.gitee.io/blog/images/Maven/mavenConstruction2.png)

解决方法：  
项目properties中maven->javaee integration去掉勾选
去除maven项目中javascript resources引用jar包
properties中project facets只选择java，去掉javascript
去除maven项目结构目录中含有web app 2.3
properties中project facets只选择java 去掉dynamic web module

# 解决maven项目名称出现个红叉报错

properties中project facets的java版本号选择不当

# 目录下的创建普通文件夹
properties中的javabuild path resource->excluded **

# pom文件war报错
1.加上webapp中的web.xml文件
2.properties中maven deployment assembly(用来配置添加根目录文件夹)不显示
	需要在project facets中修改
	
# 项目启动报错
原因jar包冲突
D:\java-tools\apache-maven-3.2.3\repo\org\apache\tomcat\servlet-api\6.0.28\servlet-api-6.0.28.jar

# xsd文件找不到
报错提示，要引入jar包。或者手动添加xsd文件或者手动指定(参考：http://www.ibm.com/developerworks/cn/xml/x-1008dubb/)

## 解决pom文件报错和multiple problems have occurred错误

错误实质上都是jar依赖的问题，只需两步就可解决
进入出问题的jar包所在的本地仓库目录
1、	删除所有以.lastUpdate结尾的文件
a)	1、切换到maven的本地仓库
b)	2、在当前目录打开cmd命令行
c)	3、执行命令：for /r %i in (*.lastUpdated) do del %i
2、	在项目上执行 Maven Update （Alt + F5）

## 如何避免jar下载不全导致项目报错

使用相对稳定的私服下载地址：开源中国(http://maven.oschina.net/help.html)
在maven指向的setting.xml文件中进行改变私服URL配置。

### 引入外部jar依赖，并使用idea的package插件打成jar包-打包

打包->JAR

外部依赖位置
![a](https://volc1612.gitee.io/blog/images/Maven/a.png)

pom引用依赖
![b](https://volc1612.gitee.io/blog/images/Maven/b.png)

```xml
<dependency>
	<groupId>Ice</groupId>
	<artifactId>Ice</artifactId>
	<version>1.0</version>
	<scope>system</scope>
	<systemPath>${basedir}/src/lib/wlfullclient.jar</systemPath>
</dependency>
```					

pom新增插件

![c](https://volc1612.gitee.io/blog/images/Maven/c.png)

```xml
<plugin>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-maven-plugin</artifactId>
	<version>1.5.2.RELEASE</version>
	<configuration>
		<includeSystemScope>true</includeSystemScope>
	</configuration>
</plugin>
```			

### linux centos安装maven

```sh
[root@linuxprobe ~]# sudo wget http://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo
[root@linuxprobe ~]# yum install apache-maven -y
```

#### 修改配置文件

```sh
[root@linuxprobe ~]# cd /usr/share/apache-maven/conf
```
```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
<localRepository>/usr/share/apache-maven/repository</localRepository>
 <mirrors>  
  <mirror>
        <id>nexus-aliyun</id>
        <mirrorOf>central</mirrorOf>
        <name>Nexus aliyun</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url> 
    </mirror>
 </mirrors>  
</settings>
```
