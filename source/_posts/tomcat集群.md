---
title: tomcat集群
date: 2017-03-05 18:51:10
update: 2017-03-05 18:51:10
categories: tomcat
tags: [tomcat, linux, 部署, nginx]
---

### 1 tomcat集群
<!-- more -->
#### 1、安装多个tomcat，每个tomcat部署同一个项目
```
# 这里的taotao-web-tomcat-01已经安装成功，并部署了项目，只要复制taotao-web-tomcat-01即可，不需要再次部署项目
# 复制递归tomcat[taotao-web-tomcat-01]
cd /ucenter/taotao-web
cp taotao-web-tomcat-01 taotao-web-tomcat-02 -R
# 做tomcat集群需要修改三个地方的配置文件
cd taotao-web-tomcat-02/conf
vim server.xml
<!-- 第一个tomcatport="8005",这里修改成8105-->
<Server port="8005" shutdown="SHUTDOWN">
  <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
  
<!-- 请求端口，第一个tomcatport="8080"，修改为8081 -->
<Connector port="8080" protocol="HTTP/1.1"
   connectionTimeout="20000"
 
<!-- 修改AJP服务端口，第一个tomcat为8009，这里修改为8109 -->
<Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />
# 保存退出
```
#### 2、使用nginx做负载均衡
```
# 进入安装nginx服务器，修改配置文件
cd /ucenter/soft/nginx/conf
vim nginx.xml
# 第一个tomcat配置
server {
	listen       80;
	server_name  www.taotao.com taotao.com;

	#charset koi8-r;

	#access_log  logs/host.access.log  main;

	proxy_set_header X-Forwarded-Host $host;
	proxy_set_header X-Forwarded-Server $host;
	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	proxy_set_header Host $host;
	location / {
		proxy_pass http://安装第一个tomcat[taotao-web-tomcat-01]所在服务器的ip:8080;
		proxy_connect_timeout 600;
		proxy_read_timeout 600;
	}
}
# 需要在http节点添加以下配置
upstream taotao-web {
	# 这里taotao-web-tomcat-01和taotao-web-tomcat-02两个tomcat都安装在了同一个服务器上
	# 8080和8081分别是两个tomcat访问的端口号
	server 安装第一个tomcat[taotao-web-tomcat-01]所在服务器的ip:8080;
	server 安装第二个tomcat[taotao-web-tomcat-02]所在服务器的ip:8081;
}
# 最后修改nginx.conf配置文件为
upstream taotao-web {
	server 安装第一个tomcat[taotao-web-tomcat-01]所在服务器的ip:8080;
	server 安装第二个tomcat[taotao-web-tomcat-02]所在服务器的ip:8081;
}

server {
	listen       80;
	server_name  www.taotao.com taotao.com;

	#charset koi8-r;

	#access_log  logs/host.access.log  main;

	proxy_set_header X-Forwarded-Host $host;
	proxy_set_header X-Forwarded-Server $host;
	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	proxy_set_header Host $host;
	location / {
		# taotao-web就是上面upstream后面的名称，相当于一个变量
		proxy_pass http://taotao-web;
		proxy_connect_timeout 600;
		proxy_read_timeout 600;
	}
}
# 保存退出nginx.conf配置文件,重启nginx
:wq
cd /ucenter/soft/nginx/conf/sbin
./ngin -s reload
```
***

```
# 需要明白一点的是nginx配合tomcat作集群，proxy_pass http://taotao-web;是采用轮询方式来代理不同的tomcat的。
# 第一次访问
# 	server 安装第一个tomcat[taotao-web-tomcat-01]所在服务器的ip:8080;
# 第二次就会访问
# 	server 安装第二个tomcat[taotao-web-tomcat-02]所在服务器的ip:8081;
# 第三次再次访问
# 	server 安装第一个tomcat[taotao-web-tomcat-01]所在服务器的ip:8080;
# 类似MySQL的主从配置后轮询读取主库和从库数据
# 如果其中一个tomcat挂掉，nginx会自动检测并从轮询列表中移除该轮询对象。
# 如果这个tomcat又正常启动了，nginx会自动检测添加该对象到轮询列表中，进行下一轮询。
```