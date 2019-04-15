---
title: memcache
date: 2016-12-12 11:48:44
update: 2016-12-12 11:48:44
categories: Memcache
tags: [memcache, linux]
---


# 安装

1.由于memcached是基于libevent的，因此需要安装libevent，libevent-devel
<!-- more  -->
```apache
yum install  libevent libevent-devel -y
```
下载：  
http://www.monkey.org/~provos/libevent-2.0.10-stable.tar.gz  
2.解压  
```apache
tar zxvf libevent-2.0.10-stable.tar.gz	
```
3.进入目录
```apache
cd libevent-2.0.10-stable
```

4.切换到root  
```apache
su
```

5.安装gcc  
```apache
yum install gcc
```

6.设置安装路径  
```apache
./configure -prefix=/usr
```

7.编译  
```apache
make
```

8.安装  
```apache
make install
```

9.测试libevent是否安装成功  

```apache
	ls -al /usr/lib | grep libevent
	lrwxrwxrwx 1 root root 21 11?? 12 17:38 libevent-1.2.so.1 -> libevent-1.2.so.1.0.3
	-rwxr-xr-x 1 root root 263546 11?? 12 17:38 libevent-1.2.so.1.0.3
	-rw-r–r– 1 root root 454156 11?? 12 17:38 libevent.a
	-rwxr-xr-x 1 root root 811 11?? 12 17:38 libevent.la
	lrwxrwxrwx 1 root root 21 11?? 12 17:38 libevent.so -> libevent-1.2.so.1.0.3
```
还不错，都安装上了。  
安装libevent参考：
	http://blog.csdn.net/zhangm168/article/details/6272801

10.安装gcc  
```apache
	yum install gcc* make*
```
11.下载memcache  
	地址:http://www.danga.com/memcached/dist/memcached-1.2.0.tar.gz
12.在/tmp解压memcache  

```apache
	tar zxvf memcached-1.4.33.tar.gz
```

安装memcached，同时需要安装中指定libevent的安装位置：  
```apache
	cd /tmp
	tar zxvf memcached-1.4.33.tar.gz
	cd memcached-1.4.33.tar.gz
	./configure –with-libevent=/usr
	make
	make install
```

如果中间出现报错，请仔细检查错误信息，按照错误信息来配置或者增加相应的库或者路径。  
安装完成后会把memcached放到 /usr/local/bin/memcached ，  
测试是否成功安装memcached：  
	
```apache
ls -al /usr/local/bin/mem*
-rwxr-xr-x 1 root root 137986 11?? 12 17:39 /usr/local/bin/memcached
-rwxr-xr-x 1 root root 140179 11?? 12 17:39 /usr/local/bin/memcached-debug
```

13.启动memcache   
```apache
	/usr/local/bin/memcached -d -m 10 -u root -l 116.62.29.229 -p 12000 -c 256 -P /tmp/memcached.pid
```
结束memcache  
```apache
kill cat /tmp/memcached.pid
```
测试：  
```apache
		[root@localhost /]# telnet 192.168.141.64 12000
		Trying 192.168.141.64...
		Connected to 192.168.141.64 (192.168.141.64).
		Escape character is '^]'.
		set key1 0 60 4
		zhou
		STORED
		get key1
		VALUE key1 0 4
		zhou
		END
```

退出：quit或者ctrl + ] 然后在按q就行了。

把Memcached服务加载到Linux的启动项中.万一机器断电系统重启.那么Memcached就会自动启动了.  
假如启动Memcache的服务器端的命令为：  

	
```apache
	/usr/local/bin/memcached -d -m 10 -u root -l 192.168.141.64 -p 12000 -c 256 -P
```

想开机自动启动的话，只需在/etc/rc.d/rc.local中加入一行，下面命令  
	
```apache
	/usr/local/memcached/bin/memcached -d -m 10 -p 12000 -u apache -c 256 
```

上面有些东西可以参考一下：即，ip不指定时，默认是本机，用户:最好选择是：apache 或 deamon  
这样，也就是属于哪个用户的服务，由哪个用户启动。  

```apache
	kill -9 "进程号"
```

参考  
配置参考：http://blog.csdn.net/u013615806/article/details/51461759  
配置参考:http://www.itgo.me/a/x2750105529827921816/memcached  


	
