---
title: Nginx
date: 2016-12-12 11:08:48
update: 2018-04-25 11:08:48
categories: Nginx
tags: [nginx, linux]
---

### Nginx
<!-- more -->
1.创建ucenter用户

一般生产环境是不会使用root用户来发布tomcat等应用的。
```
# 创建linux服务器的普通用户，用户名为ucenter，默认权限目录为home下的ucenter目录
useradd ucenter
# 进入根目录的home目录中
cd home
# 查看当前用户所处的目录
pwd
# ll会列出该文件下的所有文件信息，包括隐藏的文件，而ls -l只列出显式文件
ll
# 切换用户到ucenter
su - ucenter
# 查看当前用户所处的目录
pwd
/home/ucenter
# 创建用户，用户名为ucenter访问权限为根目录下ucenter目录
useradd ucenter -d /ucenter
# 设置ucenter用户的密码为:ucenter
passwd ucenter
# 为ucenter用户创建ucenter目录
mkdir /ucenter
# 把ucenter目录及其下所有的目录的授权给ucenter用户和其所在的组
chown ucenter:ucenter /ucenter/ -R
```

2.更新163 yum源
```
cd /etc/yum.repos.d/
mv CentOS-Base.repo CentOS-Base.repo.backup
wget http://mirrors.163.com/.help/CentOS6-Base-163.repo
[root@localhost ~]# yum clean all
[root@localhost ~]# yum makecache
[root@localhost ~]# yum update
```
或者是：http://centos.ustc.edu.cn/

3.安装rzsz
yum -y install lrzsz

4.安装nginx命令
```
yum -y install gcc-c++  
yum -y install pcre pcre-devel  
yum -y install zlib zlib-devel  
yum -y install openssl openssl—devel
mkdir /usr/local/src/nginx
cd /usr/local/src/nginx
# 线上获取ngin安装包
wget http://nginx.org/download/nginx-1.7.7.tar.gz
# 线下获取ngin安装包，使用rz命令会弹出上传选项框，选择下载好的ngin安装包进行上传到当前目录下
rz
# 解压.tar.gz安装包
tar -xvf nginx-1.7.7.tar.gz
# 删除当前目录下资源
rm -rf nginx-1.7.7.tar.gz
# 进入解压的nginx中
cd nginx-1.7.7
# 安装到/ucenter/soft/nginx目录下
mkdir -p /ucenter/soft/nginx
cd /usr/local/src/nginx/nginx-1.7.7
# 指定安装路径为/ucenter/soft/nginx用户：ucenter，组：ucenter
./configure --prefix=/ucenter/soft/nginx --user=ucenter --group=ucenter
# 源码需要编译,make进行编译
make
# 最后安装
make install
# 查看是否安装成功
cd /ucenter/soft/nginx
ll
# sbin目录一般都为可执行的文件
cd sbin
# ./执行shell脚本文件，启动nginx
./nginx
# 停止nginx
./nginx -s stop
# 重新加载配置文件，需要重启nginx
./nginx -s reload
# 查看nginx是否启动成功， | 为管道命令，管道命令的含义，前面一个命令的输出作为后面一个命令的输入
# ps -ef查看进程，其输出结果作为grep的输入内容，grep在ps -ef结果中进行查找含有nginx字符的信息再作为输出。
ps -ef | grep nginx
# 结果含有两个nginx进程
# 开放80端口
/sbin/iptables -I INPUT -p tcp --dport 80 -j ACCEPT
# 浏览器输入服务器IP,回车访问成功
# 服务器重启后80端口就会关闭，需要将防火墙的规则保存到/etc/rc.d/init.d/iptables，即端口状态保存，服务器重启不关闭防火墙状态下仍然对外开放
/etc/rc.d/init.d/iptables save
cd /ucenter
# 修改普通用户ucenter对nginx的权限,ucenter:ucenter，用户名:所属组名。
# 修改soft目录的所属，-R为递归操作，将soft及其下面的目录所属修改为ucenter组下面的ucenter用户
chown ucenter:ucenter ./soft/ -R
# 切换到普通用户ucenter
su - ucenter
cd soft/nginx/sbin
# 通过普通用户启动nginx
./ nginx
# 发现启动失败，原因是linux系统对普通进行了占用80端口的限制
# 需要类似在window系统中使用以管理员身份运行的操作，普通可以使用root权限来执行脚本
# 退出当前用户，切换到root用户
exit
cd ucenter/soft/nginx/sbin
# 先将nginx所属改回到root
chown root nginx
# 设置nginx可以以root身份执行该文件
chmod u+s nginx
su - ucenter
cd soft/nginx/sbin
./nginx
# 发现80端口已经被占用，需要停止nginx
./nginx -s stop
# 重新启动nginx
./nginx
# 普通户用启动nginx成功
```
***

之前安装linux的操作详细

### 安装
1.首先要安装 PCRE
	源文件：http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz
	参考地址：http://www.runoob.com/linux/nginx-install-setup.html
<!-- more -->
2.安装nginx
	nginx安装路径/webapp/nginx
	
启动项目报错  
```apache
	SEVERE: StandardServer.await: create[8005]: 
	java.net.BindException: Address already in use
	at java.net.PlainSocketImpl.socketBind(Native Method)
	at java.net.PlainSocketImpl.bind(PlainSocketImpl.java:383)
	at java.net.ServerSocket.bind(ServerSocket.java:328)
	at java.net.ServerSocket.<init>(ServerSocket.java:194)
	at org.apache.catalina.core.StandardServer.await(StandardServer.java:404)
	at org.apache.catalina.startup.Catalina.await(Catalina.java:676)
	at org.apache.catalina.startup.Catalina.start(Catalina.java:628)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
	at java.lang.reflect.Method.invoke(Method.java:597)
	at org.apache.catalina.startup.Bootstrap.start(Bootstrap.java:289)
	at org.apache.catalina.startup.Bootstrap.main(Bootstrap.java:414)
	Dec 10, 2016 10:14:38 AM org.apache.coyote.http11.Http11Protocol pause
```
解决修改：
	8005端口被占用了
	/webapp/tomcat_bg/conf
	修改server.xml文件中的8005端口
	<Server port="8007" shutdown="SHUTDOWN">

常用命令：
```apache
/usr/local/webserver/nginx/sbin/nginx -s reload            # 重新载入配置文件
/usr/local/webserver/nginx/sbin/nginx -s reopen            # 重启 Nginx
/usr/local/webserver/nginx/sbin/nginx -s stop              # 停止 Nginx
```

### 验证nginx是否安装成功

```apache
cd  /usr/local/nginx/sbin  
./nginx -t
```

3.nginx.conf配置
```apache

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   /webapp/webdir/share;
            index  index.html index.htm;
        }
	location /xx/ {
	     proxy_pass   http://127.0.0.1:1111/xx/;
        }
	location /xx/ {
	     proxy_pass   http://127.0.0.1:1111/;
	}
	location /xx/ {
	     proxy_pass   http://127.0.0.1:1111/;
	} 
        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```

### 配置静态资源访问

注意的是nginx默认缓存关闭。

首先大致了解下nginx工作流程：

1） 用户通过域名发出访问web服务器的请求，该域名被DNS服务器解析为反向代理服务器的IP地址；
2） 反向代理服务器接受用户的请求；
3） 反向代理服务器在本地缓存中查找请求的内容，找到后直接把内容发送给用户；
4） 如果本地缓存里没有用户所请求的信息内容，反向代理服务器会代替用户向源服务器请求同样的信息内容，并把信息内容发给用户，如果信息内容是缓存的还会把它保存到缓存中。

接下来模拟请求
nginx安装在193.112.15.102上。

#### 本地host文件

当本机访问时www.nullframe.com和static.nullframe.com域名都指向193.112.15.102服务

```
# nullframe
# 正式环境
193.112.15.1 www.nullframe.com
193.112.15.1 static.nullframe.com
```

#### 修改nginx配置文件

配置文件如下

```
worker_processes  1;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    server {
        listen       80;
        server_name  localhost;
        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
# nullframe
 server { 将www.nullframe.com域名转发到ip为http://127.0.0.1:8080，也就是访问本机8080端口的项目
 	listen       80;
 	server_name  www.nullframe.com;
 	proxy_set_header X-Forwarded-Host $host;
 	proxy_set_header X-Forwarded-Server $host;
 	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
 	location / {
 		proxy_pass http://127.0.0.1:8080; # tomcat访问地址
 	}									
 }
 server { # 将static.nullframe.com域名转发到本机的/home/lc/static/nullframe/img目录
 	listen       80;
 	server_name  static.nullframe.com;
 	proxy_set_header X-Forwarded-Host $host;
 	proxy_set_header X-Forwarded-Server $host;
 	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	location / {
 		root /home/lc/static/nullframe/img;
	}  
 }
# nullframe
}

```

#### nginx作用
##### 静态http服务
当浏览访问 http://193.112.15.102 时，没有指定端口号，其实默认端口号为80，如图
![b](https://volc1612.gitee.io/blog/images/nginx/b.png)

nginx工作流程：
1. 这时nginx正在监听80端口，并处理了这个请求。
2. nginx通过检查请求首部中的 “HOST” 字段(如图)来决定让哪个虚拟主机处理访问请求，及对应的server_name下的转发
此时匹配到server_name为loclahost的虚拟主机处理，所以加载了index.html页面。


同理当浏览器访问 http://static.nullframe.com/husky.jpg 时，nginx处理请求并转发到/home/lc/static/nullframe/img目录下的husky.jpg。

##### 反向代理

反向代理主要作用是分发请求。

反向代理思想：各服务相互调用，各服务访问的域名都应该在各自的hosts文件中都指向nginx所在的服务ip，让nginx进行转发，然后将结果返回给客户端，此时Nginx就是反向代理服务器，如图

![c](https://volc1612.gitee.io/blog/images/nginx/c.png)


如：
假如nginx安装在193.112.15.10上，配置如下：

```
server {
 	listen       80;
 	server_name  www.baidu.com;
 	proxy_set_header X-Forwarded-Host $host;
 	proxy_set_header X-Forwarded-Server $host;
 	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	location / {
 		proxy_pass http://193.112.15.12:8080;
	}  
 }
```

服务器1：193.112.15.11中配置了hosts 193.112.15.10 www.baidu.com
当服务器1发起www.baidu.com(不带端口，http请求默认是80)请求时，实际上是请求了193.112.15.10，这时nginx正在监听80，然后根据nginx配置文件，将www.baidu.com请求转发到对应的ip上,也就是请求了http://193.112.15.12:8080

##### 负载均衡

当网站访问量非常大，网站站长开心赚钱的同时，也摊上事儿了。因为网站越来越慢，一台服务器已经不够用了。于是将相同的应用部署在多台服务器上，将大量用户的请求分配给多台机器处理。同时带来的好处是，其中一台服务器万一挂了，只要还有其他服务器正常运行，就不会影响用户使用。
Nginx可以通过反向代理来实现负载均衡。

![d](https://volc1612.gitee.io/blog/images/nginx/d.jpg)

配置示例：

```
upstream myapp {
	server 192.168.20.1:8080; # 应用服务器1
	server 192.168.20.2:8080; # 应用服务器2
}
server {
	listen 80;
	location / {
		proxy_pass http://myapp;
	}
}
```

##### 虚拟主机

的网站访问量大，需要负载均衡。然而并不是所有网站都如此出色，有的网站，由于访问量太小，需要节省成本，将多个网站部署在同一台服务器上。

例如将www.aaa.com和www.bbb.com两个网站部署在同一台服务器上，两个域名解析到同一个IP地址，但是用户通过两个域名却可以打开两个完全不同的网站，互相不影响，就像访问两个服务器一样，所以叫两个虚拟主机。

配置示例：

```
server {
	listen 80 default_server;
	server_name _;
	return 444; # 过滤其他域名的请求，返回444状态码
}
server {
	listen 80;
	server_name www.aaa.com; # www.aaa.com域名
	location / {
		proxy_pass http://localhost:8080; # 对应端口号8080
	}
}
server {
	listen 80;
	server_name www.bbb.com; # www.bbb.com域名
	location / {
		proxy_pass http://localhost:8081; # 对应端口号8081
	}
}
```

在服务器8080和8081分别开了一个应用，客户端通过不同的域名访问，根据server_name可以反向代理到对应的应用服务器。

虚拟主机的原理是通过HTTP请求头中的Host是否匹配server_name来实现的，有兴趣的同学可以研究一下HTTP协议。

另外，server_name配置还可以过滤有人恶意将某些域名指向你的主机服务器

### win配置访问静态资源

```
server {
    listen       80;
    server_name  www.macalline.com;
    location / {
        proxy_pass http://127.0.0.1:8081; # 本地项目地址
        proxy_connect_timeout 600;
        proxy_read_timeout 600;
    }
    # 访问www.macalline.com/assets = F:/staticassets
    location /assets/ {
        root F:/static;
        autoindex on;
    }
}
```
http://www.macalline.com/assets = http://127.0.0.1:8081/assets = F:/static/assets

