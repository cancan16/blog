---
title: linux安装JDK
date: 2016-12-12 11:46:46
update: 2016-12-12 11:46:46
categories: JDK
tags: [jdk, linux, tomcat]
---

### 1 jdk安装
<!-- more -->
下载地址:  
	http://www.oracle.com/technetwork/cn/java/javase/downloads/jdk8-downloads-2133151-zhs.html  
修改jdk中bin目录中的访问权限  
<!-- more -->

chmod [who] [opt] [mode] 文件/目录名  

```apache
chmod a+rwx java  
```

1.修改etc/profile文件，追加一下jdk配置并保存  
```apache
	export JAVA_HOME=/usr/java/jdk1.6.0_45  
	export JAVA_BIN=/usr/java/jdk1.6.0_45/bin  
	export PATH=$PATH:$JAVA_HOME/bin  
	export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar  
	export JAVA_HOME JAVA_BIN PATH CLASSPATH  
```
```apache
	source /etc/profile
```

```apache
source /etc/profile  # 刷新系统配置文件
```

2.在这个目录下/usr/local/java/jdk1.7.0_71/bin/修改java文件权限  
```apache
chmod  777 java 
java -version 
```
  
问题  
	
```apache
Error occurred during initialization of VM  
Could not reserve enough space for object heap  
```

解决方法  
	在tomcat的bin目录下，catalina.sh文件的tomcat内存参数配置过大，超过机器可用内存总数造成，修改到适当的值即可，修改的参数   为：JAVA_OPTS="-Xms50m -Xmx60m"
  
  
 # 查看tomcat是否启动
 
命令：ps -ef|grep java 或者 ps -aux | grep tomcat
如果出现一下情况说明tomcat已启用

```apache
	root 7010 1 0 Apr19 ? 00:30:13 /usr/local/java/bin/java -Djava.util.logging.config.file=/usr/local/tomcat/conf/logging.properties -Djava.awt.headless=true -Dfile.encoding=UTF-8 -server -Xms1024m -Xmx1024m -XX:NewSize=256m -XX:MaxNewSize=256m -XX:PermSize=256m -XX:MaxPermSize=256m -XX:+DisableExplicitGC -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djava.endorsed.dirs=/usr/local/tomcat/endorsed -classpath /usr/local/tomcat/bin/bootstrap.jar -Dcatalina.base=/usr/local/tomcat -Dcatalina.home=/usr/local/tomcat -Djava.io.tmpdir=/usr/local/tomcat/temp org.apache.catalina.startup.Bootstrap start
```
如果出现一下情况说明tomcat已停用
```Apache
	root 7010 1 0 Apr19 ? 00:30:30 [java]
```
  
安装JDK和Tomcat参考  
配置参考：http://blog.csdn.net/zhuying_linux/article/details/6583096/  
配置参考：http://www.jb51.net/article/85478.htm  