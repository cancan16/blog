---
title: 无法写入cookie问题
date: 2017-03-02 23:35:45
update: 2017-03-02 23:35:45
categories: JAVA
tags: [cookie]
---
### 1 无法写入cookie原因
<!-- more -->
从request中获取域名为127.0.0.1，这样违反了浏览的安全的原则。
```java
cookie.setDomain(getDomainName(request));
```
解决方法：
nginx配置
```
  server {
     listen       80;
     server_name  cart.taotao.com;

     #charset koi8-r;

     #access_log  logs/host.access.log  main;

	 proxy_set_header X-Forwarded-Host $host;
     proxy_set_header X-Forwarded-Server $host;
     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	 # 配置代理头信息,名称为Host 值为$host变量(内置对象)，nginx传给tomcat真正的域名，
	 # 而不是http://127.0.0.1:8083。处理设置cookie时域名不符导致存不到cookie的问题
	 proxy_set_header Host $host;
     location / {
	 proxy_pass http://127.0.0.1:8086; # tomcat访问地址
	 proxy_connect_timeout 600;
	 proxy_read_timeout 600;
     }
     
  }
```
总结：
二级域名可以将cookie写入到主域名下，a.taotao.com -> taotao.com
二级域名之间不能互相写入，a.taotao.com 不能写入到 b.taotao.com