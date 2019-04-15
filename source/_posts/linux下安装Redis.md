---
title: linux下安装Redis
date: 2017-03-05 10:50:43
update: 2017-03-05 10:50:43
categories: Redis
tags: [redis, linux]
---

### 1 linux下安装Redis
<!-- more -->
#### 1、安装依赖

```bash
yum -y install cpp binutils glibc glibc-kernheaders glibc-common glibc-devel gcc make gcc-c++ libstdc++-devel tcl
```

#### 2、安装

```bash
mkdir -p /usr/local/src/redis
cd /usr/local/src/redis
wget http://download.redis.io/releases/redis-2.8.17.tar.gz
# 或者 rz 回车，从本地上传
tar -xvf redis-2.8.17.tar.gz
cd redis-2.8.17
make
make test #这个就不要执行了，需要很长时间
make install
# 安装成功
cd /ucenter/soft
mkdir redis
cd redis/
# 复制/user/local/src/redis/redis-3.0.0/redis.conf到当前目录，. 代表当前目录
cp /user/local/src/redis/redis-3.0.0/redis.conf .
# 使用redis.conf配置文件启动redis服务
redis-server redis.conf
# ctrl + c redis停止，并不是后台启动的，这里要修改redis.conf文件
vim redis.conf
# 修改daemonize yes，意思是作为后台启动
# 启动redis查看进程
redis-server redis.conf
ps -ef | grep redis
# 显示结果为，含有前面的进程号和后面的redis服务使用的端口号6379，启动成功
# 开放端口号
/sbin/iptables -I INPUT -p tcp --dport 6379 -j ACCEPT
/etc/rc.d/init.d/iptables save
/etc/init.d/iptables status
```
#### 3、测试

```bash
cd /ucenter/soft/redis
# 使用redis客户端
redis-cli
ping
# 结果为PANG,连接成功
```

### 设置redis开机启动

进入redis安装目录redis-4.0.11

```bash
[root@ip-172-31-18-100 redis-4.0.11]# utils/install_server.sh
Welcome to the redis service installer
This script will help you easily set up a running redis server

Please select the redis port for this instance: [6379] 
Selecting default: 6379
Please select the redis config file name [/etc/redis/6379.conf] 
Selected default - /etc/redis/6379.conf
Please select the redis log file name [/var/log/redis_6379.log] 
Selected default - /var/log/redis_6379.log
Please select the data directory for this instance [/var/lib/redis/6379] 
Selected default - /var/lib/redis/6379
Please select the redis executable path [/usr/local/bin/redis-server] 
Selected config:
Port           : 6379
Config file    : /etc/redis/6379.conf
Log file       : /var/log/redis_6379.log
Data dir       : /var/lib/redis/6379
Executable     : /usr/local/bin/redis-server
Cli Executable : /usr/local/bin/redis-cli
Is this ok? Then press ENTER to go on or Ctrl-C to abort.
Copied /tmp/6379.conf => /etc/init.d/redis_6379
Installing service...
Successfully added to chkconfig!
Successfully added to runlevels 345!
Starting Redis server...
Installation successful!
[root@ip-172-31-18-100 redis-4.0.11]# chkconfig redis_6379 on
[root@ip-172-31-18-100 redis-4.0.11]# chkconfig

Note: This output shows SysV services only and does not include native
      systemd services. SysV configuration data might be overridden by native
      systemd configuration.

      If you want to list systemd services use 'systemctl list-unit-files'.
      To see services enabled on particular target use
      'systemctl list-dependencies [target]'.

netconsole      0:off   1:off   2:off   3:off   4:off   5:off   6:off
network         0:off   1:off   2:on    3:on    4:on    5:on    6:off
redis_6379      0:off   1:off   2:on    3:on    4:on    5:on    6:off
[root@ip-172-31-18-100 redis-4.0.11]# Connection closing...Socket close.
```

```bash
chkconfig redis_6379 off
#开机启动
chkconfig redis_6379 on
#停止
service redis_6379 stop
#启动
service redis_6379 start
```