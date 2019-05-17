---
title: linux下安装和操作mysql
date: 2016-12-12 10:48:23
update: 2017-03-20 10:48:23
categories: MySQL
tags: [linux, mysql]
---
### 1 linux下安装mysql衍生版(性能高于原版)
<!-- more -->

补充：
发现navicat连不上服务器上的mysql
解决：
执行命令
grant all privileges on *.* to 'root'@'%' identified by '123456';
报错：
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
首先修改当前登录用户的密码:
ALTER USER USER() IDENTIFIED BY '1234567';
再执行
grant all privileges on *.* to 'root'@'%' identified by '1234567';
刷新权限：
flush  privileges ;

导入sql文件

```mysql
mysql -uroot databasefoo < foo.sql 
```
```
mysql> source d:wcnc_db.sql
```
导出整个数据库
```
mysqldump -uroot -pmysql sva_rec  > e:\sva_rec.sql 
```

***

1、首先安装cmake
```
yum -y install cmake
```
2、安装mysql
```
cd /usr/local/src/
mkdir mysql-percona
cd mysql-percona/
# 上传Percona-Server-5.6.21-70.0-r688-el6-x86_64-bundle.tar安装包
rz
# 解压
tar -xvf Percona-Server-5.6.21-70.0-r688-el6-x86_64-bundle.tar
# linux安装软件有三种，编译安装，yum安装，rpm安装。
# 安装解压后的shared，这个文件相当于window平台下.exe文件直接安装即可
rpm -ivh Percona-Server-shared-56-5.6.21-rel70.0.el6.x86_64.rpm
# 安装client
rpm -ivh Percona-Server-client-56-5.6.21-rel70.0.el6.x86_64.rpm
# 安装server
rpm -ivh Percona-Server-server-56-5.6.21-rel70.0.el6.x86_64.rpm
# 查看安装后的mysql服务状态
service mysql status
# 启动MySQL服务
service mysql start
# 登录
mysql -uroot
# 登录成功，说明安装成功后root用户是没有密码的
# 设置root用户密码
mysqladmin -u root password "root"
# 再次登录
mysql -uroot -proot
# 退出mysql
exit;
# 设置mysql端口号3306对外开放
/sbin/iptables -I INPUT -p tcp --dport 3306 -j ACCEPT
/etc/rc.d/init.d/iptables save
# 查看/etc/init.d/iptables状态信息
/etc/init.d/iptables status
# 设置远程访问（使用root密码）：
grant all privileges on *.* to 'root' @'%' identified by 'root';
flush privileges;
# navicat登录成功
```
### 2 解决客户端访问linux系统中mysql慢的问题
原因是：mysql客户端每次访问时会试图解析访问机器的hostname，并缓存到hostname cache,如果解析不了，会等待一段时间后，才能把数据取出来。
解决
```
# 编辑修改my.cnf，mysql配置文件
vim /etc/my.cnf
```
在[mysqld]下面添加：
```
# 让MySQL跳过解析机器hostname
skip-name-resolve
```
```
# 重启mysql服务
service mysql restart
```
### 3.linux下运行sql执行文件

```
cd /tmp/
# 上传sql文件到tmp临时目录
rz
# 执行taotao.sql文件,登录mysql使用taotao数据库执行cat输出的数据
cat taotao.sql | mysql -uroot -proot -Dtaotao
```

***

之前安装和操作mysql

### linux安装mysql  
1.查看服务器中是否已经安装了mysql  

```apache
ps -aux | grep "mysql"   # 查看有关mysql进程
rpm -qa | grep mysql     # 用于查看linux服务器是否安装了默认的mysql
rpm -e mysql　　         # 普通删除模式
rpm -e --nodeps mysql　　# 强力删除模式，如果使用上面命令删除时，提示有依赖的其它文件，则用该命令可以对其进行强力删除  
rpm -e mysql-5.7.13-linux-glibc2.5-x86_64 --nodeps  # 卸载已经安装的mysql-5.7.13-linux-glibc2.5-x86_64数据库
```
2.解压mysql源码文件  

下载mysql：网址:http://downloads.mysql.com/archives/community/  
	这里下载mysql-5.7.12-linux-glibc2.5-x86_64.tar版本  
	把下载好的mysql安装文件放到/home/mysql中，如果没有mysql文件夹先创建，再放到里面  
```apache
cd /home/mysql  # 进入mysql目录准备解压mysql源文件
tar -zxf mysql-5.7.12-linux-glibc2.5-x86_64.tar.gz -C /home/mysql   # 解压到/home/mysql文件夹下
```
		
3.创建mysql的用户组/用户, data目录及其用户目录  
```apache
 userdel mysql                                           # 删除用户
 groupdel mysql                                          # 删除用户组名
 mkdir /home/mysql                                       # 在home文件夹下创建文件夹mysql
 mkdir /home/mysql/data                                  # 在mysql文件夹下创建文件夹data  
 groupadd mysql                                          # 创建一个名为mysql的用户组       
 useradd -g mysql -d /home/mysql mysql                   # 在用户组下创建用户
[root@VM_0_11_redhat /]# useradd -g mysql -d /home/mysql lcmysql
useradd: user 'lcmysql' already exists

```
useradd 命令语法：
useradd [-mMnr][-c <备注>][-d <登入目录>][-e <有效期限>][-f <缓冲天数>][-g <群组>][-G <群组>][-s <shell>][-u <uid>][用户帐号]

4.初始化mysql数据库  
```apache
cd /home/mysql                                         # 进入安装目录
./bin/mysql_install_db  --user=mysql --basedir=/home/mysql --datadir=/home/mysql/data 
```
mysql5.7新特性：由上面可以看出， mysql_install_db 已经不再推荐使用了，建议改成 mysqld --initialize 完成实例初始化。  

```apache
./bin/mysqld --user=mysql --basedir=/home/mysql --datadir=/home/mysql/data --initialize
./mysqld --user=mysql --basedir=/home/mysql --datadir=/home/mysql/data --initialize
[Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
[ERROR] --initialize specified but the data directory has files in it. Aborting. 2016-04-08T01:46:53.155879Z 0
[ERROR] Aborting
```
出现上面的错误是因为我们没有清空mysql的data目录， 执行清空命令如下：  

```apache
cd /home/mysql/data                        # 进入安装目录下的data目录
rm -fr *                                   # 清空数据
cd /home/mysql      
./bin/mysqld --user=mysql --basedir=/home/mysql --datadir=/home/mysql/data --initialize
[Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details). 2016-04-08T01:47:59.945537Z 0
[Warning] InnoDB: New log files created, LSN=45790 2016-04-08T01:48:00.333528Z 0 [Warning] InnoDB: Creating foreign key constraint system tables. 2016-04-08T01:48:00.434908Z 0
[Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: ece26421-fd2b-11e5-a1e3-00163e001e5c. 2016-04-08T01:48:00.440125Z 0
[Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened. 2016-04-08T01:48:00.440904Z 1
[Note] A temporary password is generated for root@localhost: 2EC+qfcossJ=
```
初始化数据库遇到的问题
```
[root@VM_0_11_redhat mysql-8.0]# ./bin/mysqld --user=lcmysql --basedir=/home/mysql-8.0/mysql --datadir=/home/mysql/mysql-8.0/data --initialize
./bin/mysqld: error while loading shared libraries: libnuma.so.1: cannot open shared object file: No such file or directory
```
解决：
安装numactl(numactl - 用于控制 进程与共享存储的 NUMA 技术机制)程序
```
[root@VM_0_11_redhat mysql-8.0]# yum -y install numactl
```
安装成功后再次初始化mysql
```
[root@VM_0_11_redhat mysql-8.0]# ./bin/mysqld --user=lcmysql --basedir=/home/mysql-8.0/mysql --datadir=/home/mysql/mysql-8.0/data --initialize
2018-03-28T14:56:44.107771Z 0 [Note] Basedir set to /home/mysql/mysql-8.0/
2018-03-28T14:56:44.107993Z 0 [Warning] The syntax '--symbolic-links/-s' is deprecated and will be removed in a future release
2018-03-28T14:56:44.108114Z 0 [ERROR] Can't find error-message file '/home/mysql-8.0/mysql/share/errmsg.sys'. Check error-message file location and 'lc-messages-dir' configuration directive.
2018-03-28T14:56:44.122252Z 0 [Warning] You need to use --log-bin to make --log-slave-updates work.
2018-03-28T14:57:10.137438Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 4a83def8-3298-11e8-a09d-5254000f41f8.
2018-03-28T14:57:10.142916Z 0 [Warning] You have not provided a mandatory server-id. Servers in a replication topology must have unique server-ids. Please refer to the proper server start-up parameters documentation.
2018-03-28T14:57:10.144545Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2018-03-28T14:57:10.145308Z 5 [Note] A temporary password is generated for root@localhost: tlQl0#pAj13j
```
其中有个报错信息
```
2018-03-28T14:56:44.108114Z 0 [ERROR] Can't find error-message file '/home/mysql-8.0/mysql/share/errmsg.sys'. Check error-message file location and 'lc-messages-dir' configuration directive.
```
解决方法：
修改/etc/my.cnf文件，添加language的配置项
添加内容如下
```
# language的配置项
language=/home/mysql/mysql-8.0/share/english
```
牢记上面的随机密码， 如上**mjT,#x_5sW, 下面我们修改密码时需要用到。  

5.检测下是否能启动mysql服务  
```
[root@VM_0_11_redhat mysql-8.0]# cd /home/mysql
./support-files/mysql.server start
Starting MySQL.. OK!
```
---
如果出现以下问题，需要修改/support-files/mysql.server  
```
[root@VM_0_11_redhat mysql-8.0]# ./support-files/mysql.server start
./support-files/mysql.server: line 239: my_print_defaults: command not found
./support-files/mysql.server: line 259: cd: /usr/local/mysql: No such file or directory
Starting MySQLCouldn't find MySQL server (/usr/local/mysql/[FAILED]ld_safe)
```
修改/home/mysql/support-files/mysql.server文件中的basedir和datadir路径

```
[root@VM_0_11_redhat mysql-8.0]# vim support-files/mysql.server
```
添加如下内容
```
basedir=/home/mysql/mysql-8.0
datadir=/home/mysql/mysql-8.0/data
```
```
[root@VM_0_11_redhat mysql-8.0]# ./support-files/mysql.server start
./support-files/mysql.server: line 240: my_print_defaults: command not found
./support-files/mysql.server: line 260: cd: /usr/local/mysql: No such file or directory
Starting MySQLCouldn't find MySQL server (/usr/local/mysql/[FAILED]ld_safe)
```
解决方法：
编辑/etc/my.cnf 文件，因为缺少basedir 和 datadir 两个路径，在 [mysqld] 添加上即可
添加如下内容

```
# mysql的安装路径
basedir=/home/mysql/mysql-8.0
# mysql的数据路径
datadir=/home/mysql/mysql-8.0/data
```

启动再次报错

```
[root@VM_0_11_redhat mysql-8.0]# ./support-files/mysql.server start
Starting MySQL.2018-03-28T15:47:01.888599Z mysqld_safe error: log-error set to '/home/mysql/mysql-8.0/logs/error.log', however file don't exists. Create writable for user 'mysql'.
The server quit without updating PID file (/home/mysql/mysq[FAILED]ta/VM_0_11_redhat.pid).
```

解决方法：
创建logs目录和error.log文件

```
[root@VM_0_11_redhat mysql-8.0]# mkdir logs
[root@VM_0_11_redhat logs]# cat >> error.log
```
按Ctrl + d 保存并退出

启动报错：

```
[root@VM_0_11_redhat mysql-8.0]# ./support-files/mysql.server start
Starting MySQL.2018-03-28T15:51:24.044893Z mysqld_safe Directory '/var/lib/mysql' for UNIX socket file don't exists.
The server quit without updating PID file (/home/mysql/mysq[FAILED]ta/VM_0_11_redhat.pid).
```
解决方法：
在/home/mysql/mysql-8.0/data中创建 mysql.socket文件,并修改/etc/my.cnf中socket的路径

```
[root@VM_0_11_redhat data]# cat >> mysql.socket
```
报错：
```
[root@VM_0_11_redhat mysql-8.0]# ./support-files/mysql.server start
The server quit without updating PID file (/home/mysql/mysq[FAILED]ta/VM_0_11_redhat.pid).
```

登录时问题：
```
[root@VM_0_11_redhat mysql-8.0]# ./support-files/mysql.server start
Starting MySQL                                             [  OK  ]
[root@VM_0_11_redhat mysql-8.0]# vim /etc/my.cnf
[root@VM_0_11_redhat mysql-8.0]# mysql -u root - p
-bash: mysql: command not found
[root@VM_0_11_redhat mysql-8.0]# 
```

原因：
这个是因为/usr/local/bin目录下缺失mysql导致，只需要一下方法建立软链接，即可以解决：
把mysql安装目录，比如MYSQLPATH/bin/mysql，映射到/usr/local/bin目录下： 

```
[root@VM_0_11_redhat bin]# pwd
/home/mysql/mysql-8.0/bin
[root@VM_0_11_redhat bin]# cd /usr/local/bin
[root@VM_0_11_redhat bin]# ln -s /home/mysql/mysql-8.0/bin/mysql /usr/bin
[root@VM_0_11_redhat bin]# mysql -u root
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)
```
重新杀掉mysql进程，并重启服务遇到的问题

```
[root@VM_0_11_redhat mysql-8.0]# ./support-files/mysql.server start
Starting MySQL.The server quit without updating PID file (/[FAILED]ql/mysql-8.0/data/VM_0_11_redhat.pid).

```
解决：
授权
#chown -R lcmysql:mysql /home/mysql/mysql-8.0
chown -R lcmysql.mysql /home/mysql/mysql-8.0
chown -R lcmysql:mysql /home/mysql/mysql-8.0


**修改后的my.cnf文件**

```
[mysqld]
# mysql的安装路径
basedir=/home/mysql/mysql-8.0
# mysql的数据路径
datadir=/home/mysql/mysql-8.0/data
socket=/var/lib/mysql/mysql.sock
# language的配置项
language=/home/mysql/mysql-8.0/share/english
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# Settings user and group are ignored when systemd is used.
# If you need to run mysqld under a different user or group,
# customize your systemd unit file for mariadb according to the
# instructions in http://fedoraproject.org/wiki/Systemd

[mysqld_safe]
log-error=/var/log/mariadb/mariadb.log
pid-file=/var/run/mariadb/mariadb.pid

#
# include all files from the config directory
#
!includedir /etc/my.cnf.d
```
---

6.创建软链接  
```apache
ln -s /home/mysql/bin/mysql /usr/bin/mysql
```

7.创建配置文件  
	1、将默认生成的my.cnf备份  
```apache
mv /etc/my.cnf /etc/my.cnf.bak
```
如果这一步报错，没有关系，正常情况下是由于/ect文件夹下没有my.cnf锁引起的，创建一下就可以了，或者你不会创建 ， 直接使用 vim   
/ect/my.cnf 命令编辑 ，然后直接退出编辑就可以，在执行这个命令就不会出错了，退出编辑命令  esc键  -> (shift键+ ：(冒号键) -> wq! ),然后在执行上面的命令就不会报错了。  
	
2、进入mysql的安装目录支持文件目录  

```apache
cd /home/mysql/support-files
```
3、拷贝配置文件模板为新的mysql配置文件
	
```apache
cp my-default.cnf /etc/my.cnf
```
4、设置编码，可按需修改新的配置文件选项， 不修改配置选项， mysql则按默认配置参数运行  
如下是我修改配置文件/etc/my.cnf， 设置编码为utf8以防乱码  

```apache
	vim /etc/my.cnf
	 
	[mysqld]
	 
	basedir = /home/mysql
	datadir = /home/mysql/data
	 
	character_set_server=utf8
	init_connect='SET NAMES utf8'
	 
	 
	[client]
	default-character-set=utf8
```

有的时候使用这个命令后，前3步有问题的这里可能my.cnf文件是空的，这个不要紧，既然没有生成，那么你就自己编辑喽！具体编辑怎么实现就参照步骤6  

8.配置mysql服务开机自动启动  
```apache
		cp /home/mysql/support-files/mysql.server /etc/init.d/mysqld      # 拷贝启动文件到/etc/init.d/下并重命令为mysqld
		chmod 755 /etc/init.d/mysqld                                      # 增加执行权限
		chkconfig --list mysqld                                           # 检查自启动项列表中没有mysqld这个，
		chkconfig --add mysqld                                            # 如果没有就添加mysqld：
		chkconfig mysqld on                                               # 用这个命令设置开机启动：
```
如果在执行第一步就报错，则说明刚刚在第8步中配置出问题了，那你就重新设置一下  
	
![1](https://volc1612.gitee.io/blog/images/linux操作mysql/1.png)
如果后面的操作中出现以下的问题  
![2](https://volc1612.gitee.io/blog/images/linux操作mysql/2.png)
	
这样就是电脑环境的问题了，你就直接把  
![3](https://volc1612.gitee.io/blog/images/linux操作mysql/3.png)
	
	
9.mysql服务的启动/重启/停止  
```apache
		service mysqld start                                               # 启动服务
		service mysqld restart                                             # 重启服务
		service mysqld stop                                                # 停止服务
```
		
10.修改mysql登录密码  
```apache
		mysql> SET PASSWORD = PASSWORD('123456'); # PASSWORD()里面的123456是我设置的新密码，你也可以设置成你的密码
		Query OK, 0 rows affected, 1 warning (0.00 sec)
		Rows matched: 2 Changed: 0 Warnings: 1
		mysql> ALTER USER 'root'@'localhost' PASSWORD EXPIRE NEVER;
		Query OK, 0 rows affected, 1 warning (0.00 sec)
		Rows matched: 2 Changed: 0 Warnings: 1
		mysql> flush privileges;
		Query OK, 0 rows affected, 1 warning (0.00 sec)
		Rows matched: 2 Changed: 0 Warnings: 1
```
11.mysql远程授权  
```apache
		[root@testfornss ~]# mysql -u root -p  
		Enter password:
		Welcome to the MySQL monitor. Commands end with ; or \g.
		Your MySQL connection id is 4
		Server version: 5.7.13 MySQL Community Server (GPL)
		 
		Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.
		 
		Oracle is a registered trademark of Oracle Corporation and/or its
		affiliates. Other names may be trademarks of their respective
		owners.
		 
		Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
		 
		mysql> grant all privileges on *.* to 'root'@'%' identified by '123456';
		Query OK, 0 rows affected, 1 warning (0.00 sec)
		 
		mysql>
```
12.设置访问mysql数据库不区分大小写  
	修改目录下/etc的my.cnf  
	在[mysqld]配置下追加  
```apache
	lower_case_table_names=1  
```
0代表区别大小写  
	
问题：  
```apache
		mysql -u bg -p # 回车输入密码连不上
		mysql -r bg -p # 回车输入密码可以连上
```
解决方法：  
properties配置数据库properties配置数据库：
```apache
		master.name=csp
		master.url=jdbc:mysql://116.62.29.200:3306/bg?characterEncoding=utf-8&allowMultiQueries=true
		master.driverClassName=com.mysql.jdbc.Driver

		# \u5f00\u53d1\u73af\u5883
		master.username=root           # 名称是root，不是bg
		master.password=bmx84185858
```
	
参考资源：http://www.cnblogs.com/gaojupeng/p/5727069.html
		
### 登录mysql

```apache
	mysql -u root -p  	# 登录
	quit;  				# 退出
```




### 开启查询日志
```mysql
show variables like '%quer%';
```
结果：
```mysql
+----------------------------------------+--------------------------------------------------------+
| Variable_name                          | Value                                                  |
+----------------------------------------+--------------------------------------------------------+
| binlog_rows_query_log_events           | OFF                                                    |
| ft_query_expansion_limit               | 20                                                     |
| have_query_cache                       | YES                                                    |
| log_queries_not_using_indexes          | OFF                                                    |
| log_throttle_queries_not_using_indexes | 0                                                      |
| long_query_time                        | 10.000000                                              |
| query_alloc_block_size                 | 8192                                                   |
| query_cache_limit                      | 1048576                                                |
| query_cache_min_res_unit               | 4096                                                   |
| query_cache_size                       | 1048576                                                |
| query_cache_type                       | OFF                                                    |
| query_cache_wlock_invalidate           | OFF                                                    |
| query_prealloc_size                    | 8192                                                   |
| slow_query_log                         | OFF                                                    |
| slow_query_log_file                    | /usr/local/mysql/data/iZbp199af3f3y8j7fr8kolZ-slow.log |
+----------------------------------------+--------------------------------------------------------+
15 rows in set (0.00 sec)
```
Linux下找到mysql的配置文件my.cnf, 在mysqld下方加入慢查询的配置语句(注意:一定要在[mysqld]下的下方加入)

### 重启mysql服务的报错
```msyql
[root@iZbp199af3f3y8j7fr8kolZ /]# service mysqld start
Starting MySQL...The server quit without updating PID file [FAILED]cal/mysql/data/iZbp199af3f3y8j7fr8kolZ.pid).
```
解决方法:
配置my.cnf文件
```mysql
# 设置不区分大小写
 lower_case_table_names=1
 
# 开启慢查询
  slow_query_log=on

# 设置多少时长为慢查询
 long_query_time=0.00001
 
# 错误日志
 log-error=/usr/local/mysql/logs/error.log
# 慢查寻信息日志
 slow_query_log_file=/usr/local/mysql/logs/slowquery.log
 
# log=/usr/local/mysql/logs/mysql.log
# log_slow_queries=/usr/local/mysql/logs/slowquery.log
# mysql5.6版本以上，取消了参数log_slow_queries，更改为slow_query_log_file
# log_slow_queries=/home/db/madb/log/slow_query.log
```
mysql获取随机数更新百万数据  

```mysql
SELECT FLOOR( MAX(NOW()) * RAND()) as r;
```
重启mysql服务
```mysql
sudo /usr/local/mysql/support-files/mysql.server restart
```
查询多长时间为慢查询

```mysql
show variables like 'long_query_time';
```
查询输出记录次数最多的10条SQL语句
```mysql
/usr/local/mysql/bin/mysqldumpslow -s c -t 10 /usr/local/mysql/logs/slowquery.log
```
/usr/local/mysql/bin/mysqldumpslow -s t -t 2 -a /usr/local/mysql/logs/slowquery.log


如果正在运行的是线上库，不方便重启   

MySQL从5.1及以后版本支持了 开启slow query in runtime。只需要执行：  

```mysql-5
  mysql> SET GLOBAL slow_query_log = 'ON';
```

如果不生效，试一下

```mysql
mysql> FLUSH LOGS;
```

### 卸载mysql

#### 检查是否安装了MySQL组件

```
[root@VM_0_11_redhat /]# rpm -qa | grep -i mysql
perl-DBD-MySQL-4.023-5.el7.x86_64
```
说明安装了mysql相关组件

#### 卸载前关闭MySQL服务
##### 方法1：
```
[root@DB-Server init.d]# service mysql status
 MySQL running (25673)[  OK  ]
[root@DB-Server init.d]# service mysql stop
 Shutting down MySQL..[  OK  ]
[root@DB-Server init.d]# service mysql status
 MySQL is not running[FAILED]
```
##### 方法2：
```
[root@DB-Server init.d]# ./mysql status
 MySQL running (26215)[  OK  ]
[root@DB-Server init.d]# ./mysql stop
 Shutting down MySQL..[  OK  ]
[root@DB-Server init.d]# ./mysql status
 MySQL is not running[FAILED]
[root@DB-Server init.d]# chkconfig --list | grep -i mysql
mysql 0:off 1:off 2:on 3:on 4:on 5:on 6:off
[root@DB-Server init.d]# 
```
#### 收集MySQL对应的文件夹信息

```
[root@DB-Server init.d]# whereis mysql
mysql: /usr/bin/mysql /usr/include/mysql /usr/share/mysql /usr/share/man/man1/mysql.1.gz
```

最好实用find命令查看MySQL数据库相关的文件，方便后面彻底删除MySQL。

```
[root@DB-Server init.d]# find / -name mysql
/etc/rc.d/init.d/mysql
/etc/logrotate.d/mysql
/var/lock/subsys/mysql
/var/lib/mysql
/var/lib/mysql/mysql
/usr/include/mysql
/usr/include/mysql/mysql
/usr/bin/mysql
/usr/share/mysql
/usr/lib64/mysql	
```
#### 卸载删除MySQL各类组件

```
[root@DB-Server init.d]# rpm -ev perl-DBD-MySQL-4.023-5.el7.x86_64
```

#### 删除MySQL对应的文件夹

检查各个MySQL文件夹是否清理删除干净。

```
[root@DB-Server init.d]# whereis mysql
mysql:
[root@DB-Server init.d]# find / -name mysql
/var/lib/mysql
/var/lib/mysql/mysql
/usr/lib64/mysql
[root@DB-Server init.d]# rm -rf /var/lib/mysql
[root@DB-Server init.d]# rm -rf /var/lib/mysql/mysql
[root@DB-Server init.d]# rm -rf /usr/lib64/mysql
[root@DB-Server init.d]# 
```

#### 删除mysql用户及用户组

如果有必要，删除mysql用户以及mysql用户组。

```
[root@DB-Server ~]# more /etc/passwd | grep mysql
mysql:x:101:501::/home/mysql:/bin/bash
[root@DB-Server ~]# more /etc/shadow | grep mysql
mysql:!!:16496::::::
[root@DB-Server ~]# more /etc/group | grep mysql
mysql:x:501:
[root@DB-Server ~]# userdel mysql
[root@DB-Server ~]# groupdel mysql
groupdel: group mysql does not exist
[root@DB-Server ~]# 
```

#### 确认MySQL是否卸载删除

```
[root@DB-Server init.d]# rpm -qa | grep -i mysql
```

### 二进制包/源码安装方式的MySQL卸载

如果是采用二进制包安装的MySQL，那么你用下面命令是找不到任何MySQL组件的。所以如果你不知道MySQL的安装方式，千万不要用下面命令来判别是否安装了MySQL

```
[root@DB-Server init.d]# rpm -qa | grep -i mysql
```

#### 检查MySQL服务并关闭服务进程。

首先通过进程查看是否有MySQL的服务的状态, 如下所示，MySQL服务是启动的。

```
[root@DB-Server init.d]# ps -ef | grep mysql
root      4752  4302  0 22:55 pts/1    00:00:00 more /etc/init.d/mysql.server
root      7176     1  0 23:23 pts/1    00:00:00 /bin/sh /usr/local/mysql/bin/mysqld_safe --datadir=/usr/local/mysql/data --pid-file=/usr/local/mysql/data/DB-Server.localdomain.pid
mysql     7269  7176 15 23:23 pts/1    00:00:01 /usr/local/mysql/bin/mysqld --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data --plugin-dir=/usr/local/mysql/lib/plugin --user=mysql --log-error=/usr/local/mysql/data/DB-Server.localdomain.err --pid-file=/usr/local/mysql/data/DB-Server.localdomain.pid
root      7321  4302  0 23:23 pts/1    00:00:00 grep mysql
[root@DB-Server init.d]# /etc/init.d/mysql.server status
MySQL running (7269)[  OK  ]
[root@DB-Server init.d]# /etc/init.d/mysql.server stop
Shutting down MySQL..[  OK  ]
[root@DB-Server init.d]# /etc/init.d/mysql.server status
MySQL is not running[FAILED]
[root@DB-Server init.d]# 
```
#### 查找MySQL的安装目录并彻底删除

```
[root@DB-Server init.d]# whereis mysql
 
mysql: /usr/local/mysql
 
[root@DB-Server init.d]# find / -name mysql
 
/var/spool/mail/mysql
 
/usr/local/mysql-5.7.5-m15-linux-glibc2.5-x86_64/include/mysql
 
/usr/local/mysql-5.7.5-m15-linux-glibc2.5-x86_64/bin/mysql
 
/usr/local/mysql-5.7.5-m15-linux-glibc2.5-x86_64/data/mysql
 
/usr/local/mysql

[root@DB-Server init.d]# rm -rf /usr/local/mysql-5.7.5-m15-linux-glibc2.5-x86_64/
 
[root@DB-Server init.d]# rm -rf /usr/local/
 
[root@DB-Server init.d]# rm -rf /var/spool/mail/mysql
 
[root@DB-Server init.d]# 
```

#### 删除一些配置文件

配置文件一般有/etc/my.cnf 或/etc/init.d/mysql.server，视具体安装配置情况而定。


#### 删除MySQL用户以及用户组

```
[root@DB-Server ~]# id mysql
 
uid=101(mysql) gid=501(mysql) groups=501(mysql) context=root:system_r:unconfined_t:SystemLow-SystemHigh
 
[root@DB-Server ~]# userdel mysql
```

### 安装mysql

#### 查看环境：
```
[root@VM_0_11_redhat etc]# cat /etc/os-release
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"

[root@VM_0_11_redhat etc]# getconf LONG_BIT
64
[root@VM_0_11_redhat etc]# 
```
结果来看是CentOS-7-64位的
#### 在线安装mysql
##### 配置MySQL8.0的安装源

```
[root@VM_0_11_redhat etc]# rpm -Uvh https://repo.mysql.com/mysql57-community-release-el7-11.noarch.rpm
```

#### 安装MySQL 8.0社区版服务以及客户端工具

```
[root@VM_0_11_redhat etc]# yum --enablerepo=mysql80-community install mysql-community-server
```

#### 启动Mysql服务

```
[root@VM_0_11_redhat etc]# service mysqld start
Redirecting to /bin/systemctl start  mysqld.service
```

#### 查看状态

```
[root@VM_0_11_redhat etc]# service mysqld status
Redirecting to /bin/systemctl status  mysqld.service
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2018-04-17 23:30:37 CST; 16min ago
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
  Process: 22740 ExecStart=/usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid $MYSQLD_OPTS (code=exited, status=0/SUCCESS)
  Process: 22723 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
 Main PID: 22744 (mysqld)
   CGroup: /system.slice/mysqld.service
           └─22744 /usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid

Apr 17 23:30:35 VM_0_11_redhat systemd[1]: Starting MySQL Server...
Apr 17 23:30:37 VM_0_11_redhat systemd[1]: Started MySQL Server.
[root@VM_0_11_redhat etc]# 

```
#### 关闭Mysql服务

```
[root@VM_0_11_redhat etc]# service mysqld stop
Redirecting to /bin/systemctl stop  mysqld.service
[root@VM_0_11_redhat etc]# 
```

#### 查看mysql为root用户生成的临时密码

```
[root@VM_0_11_redhat etc]# grep "A temporary password" /var/log/mysqld.log
2018-04-17T14:07:09.670864Z 5 [Note] [MY-010454] A temporary password is generated for root@localhost: zeIx9G2kvn%C
```

#### Mysql安装配置,根据提示设置新的密码和密码策略

```
[root@VM_0_11_redhat etc]# mysql_secure_installation
```

![4](https://volc1612.gitee.io/blog/images/linux操作mysql/4.png)

#### 开放mysql端口

需要注意以下问题：

如果防火墙关闭时，服务器应用的端口号都是开放的
如果防火墙开启时，需要在防火墙中配置开放的端口号

CentOS中防火墙程序主要是firewall和iptables
CentOS7下使用iptables会报错

```
[root@VM_0_11_redhat home]# systemctlrestart iptables.service
-bash: systemctlrestart: command not found
```
systemctlrestart iptables.service # 最后重启防火墙使配置生效
systemctlenable iptables.service # 设置防火墙开机启动

在CentOS7下使用firewall
##### 查看防火墙状态

```
[root@VM_0_11_redhat home]# firewall-cmd --state
not running
```

##### 关闭防火墙

```
[root@VM_0_11_redhat home]# systemctl stop firewalld.service
```

##### 开启防火墙

会遇到Failed to start firewalld.service: Unit is masked.的错误

```
[root@VM_0_11_redhat home]# systemctl start firewalld.service
[root@VM_0_11_redhat ~]# systemctl start firewalld
Failed to start firewalld.service: Unit is masked.
[root@VM_0_11_redhat ~]# systemctl unmask firewalld
Removed symlink /etc/systemd/system/firewalld.service.
```

##### 禁止开机启动启动防火墙

```
[root@VM_0_11_redhat home]# systemctl disable firewalld.service
```
##### 开放3306端口号

```
[root@VM_0_11_redhat home]# firewall-cmd --add-port=3306/tcp --permanent 

[root@VM_0_11_redhat home]# firewall-cmd --reload

[root@VM_0_11_redhat etc]# firewall-cmd --list-ports
3306/tcp 8080/tcp
[root@VM_0_11_redhat etc]# 
```

##### 防火墙中3306端口号不开放

在防火墙中关闭3360端口号首先需要关闭mysql服务

```
[root@VM_0_11_redhat etc]# firewall-cmd --zone-public --remove-port=3306/tcp --permanent
usage: see firewall-cmd man page
firewall-cmd: error: unrecognized arguments: --zone-public
[root@VM_0_11_redhat etc]# service mysqld stop
Redirecting to /bin/systemctl stop  mysqld.service
[root@VM_0_11_redhat etc]# firewall-cmd --zone=public --remove-port=3306/tcp --permanent
success
```

##### 查询3306端口号是否已被应用占用

```
[root@VM_0_11_redhat etc]# service mysqld stop
Redirecting to /bin/systemctl stop  mysqld.service
[root@VM_0_11_redhat etc]# lsof -i:3306
[root@VM_0_11_redhat etc]# service mysqld start
Redirecting to /bin/systemctl start  mysqld.service
[root@VM_0_11_redhat etc]# lsof -i:3306
COMMAND   PID  USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
mysqld  24915 mysql   17u  IPv6 3798680      0t0  TCP *:mysql (LISTEN)
[root@VM_0_11_redhat etc]# 
```

#### Mysql客户端登录

```
[root@VM_0_11_redhat etc]# mysql -uroot -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 22
Server version: 8.0.4-rc-log MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.01 sec)

mysql> 

```

#### Mysql设置远程用户访问,当然这里也可以创建新的用户

```
[root@VM_0_11_redhat etc]# mysql> grant all privileges on *.* to 'root'@'%' identified by '12345678';
```

#### 客户端连接数据库报错

```
Authentication plugin 'caching_sha2_password' cannot be loaded: dlopen(/usr/local/mysql/lib/plugin/caching_sha2_password.so, 2): image not found 
```

```
mysql> show global variables like 'default_authentication_plugin';
+-------------------------------+-----------------------+
| Variable_name                 | Value                 |
+-------------------------------+-----------------------+
| default_authentication_plugin | caching_sha2_password |
+-------------------------------+-----------------------+
1 row in set (0.00 sec)

mysql> 

```

root用户 在安装数据库是，指定的加密插件是：caching_sha2_password，需要把root用户的密码加密方式改成mysql_native_password

```
mysql> ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'root';
```

在CentOS7下使用在线安装的方式安装mysql8，默认已经配置了随服务重启自动启动mysql服务。

到此linux安装mysql8已经完成

#### 创建用户和授权

使用root用户在navicat客户端创建用户时报错

```
CREATE USER 'lcmysql'@'183.208.23.117' IDENTIFIED WITH mysql_native_password BY '123456lcMy.';

[SQL]CREATE USER 'lcmysql'@'183.208.20.104' IDENTIFIED WITH mysql_native_password BY 'lcMYSQL_*123';
受影响的行: 0
时间: 0.062s

[Err] 1055 - Expression #1 of ORDER BY clause is not in GROUP BY clause and contains nonaggregated column 'information_schema.PROFILING.SEQ' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by
```

这是因为MySQL 5.7.5和up实现了对功能依赖的检测。如果启用了only_full_group_by SQL模式(在默认情况下是这样)，那么MySQL就会拒绝选择列表、条件或顺序列表引用的查询，这些查询将引用组中未命名的非聚合列，而不是在功能上依赖于它们。(在5.7.5之前，MySQL没有检测到功能依赖项，only_full_group_by在默认情况下是不启用的

解决方式：
查看sql_mode值
```
mysql> select version(), @@sql_mode;
+--------------+-------------------------------------------------------------------------------------------------------------------------------------------+
| version()    | @@sql_mode                                                                                                                                |
+--------------+-------------------------------------------------------------------------------------------------------------------------------------------+
| 8.0.4-rc-log | ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION |
+--------------+-------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.01 sec)

mysql> SET sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));
Query OK, 0 rows affected (0.00 sec)

mysql> 

```

以上使用sql修改sql_mode的值只是临时生效，永久生效需修改my.cnf配置

在[mysqld]下增加如下配置，保存并重启mysql服务

```
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

使用客户端赋权会报错

```
报错：
[SQL]GRANT ALL ON lcmysql.* TO 'lcmysql'@'183.208.23.117';
[Err] 1044 - Access denied for user 'root'@'%' to database 'lcmysql'
```

所以创建用户需要使用root用户在linux下登录mysql进行操作

##### 创建用户
创建lcmysql用户，并指定在ip为183.208.11.117下使用lcmysql，使用mysql_native_password方式加密密码123456lcMy

```
mysql> CREATE USER 'lcmysql'@'183.208.11.117' IDENTIFIED WITH mysql_native_password BY '123456lcMy.';
Query OK, 0 rows affected (0.01 sec)
```

##### 授权

ip为183.208.11.117网络下让lcmysql用户可对lcmysql数据库下的所有数据拥有所有可操作权限

```
mysql> GRANT ALL ON lcmysql.* TO 'lcmysql'@'183.208.11.117';
Query OK, 0 rows affected (0.01 sec)
```
##### 刷新权限
```
mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
mysql> 
```
#### 删除用户

```
mysql> DROP USER 'lcmysql'@'183.208.11.117';
Query OK, 0 rows affected (0.00 sec)
```

#### 撤销用户权限

撤销用户查询权限

```
mysql> REVOKE SELECT ON 'lcmysql'.* FROM 'lcmysql'@'183.208.11.117';
Query OK, 0 rows affected (0.00 sec)
```


#### 更改用户密码

```
mysql> SET PASSWORD FOR 'lcmysql'@'183.208.11.117' = PASSWORD("1234567");
Query OK, 0 rows affected (0.00 sec)
```

### Linux远程连接mysql命令

mysql -h[ip] -p[port] -u[username] -p[password]

```bash
[root@izbp1d9e2n6iw6mvtx07jmz ~]# mysql -h127.0.0.1 -p3306 -uroot -proot
```
