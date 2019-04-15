---
title: MySQL主从读写分离
date: 2017-01-05 19:35:20
update: 2017-03-04 12:24:20
categories: MySQL
tags: [mysql, 主从, 读写分离]
---

### 1 mysql简单配置主从参考方式
<!-- more -->
注意：
1、	主DB server和从DB server数据库的版本一致。
2、	主DB server和从DB server数据库数据一致[ 这里就会可以把主的备份在从上还原，也可以直接将主的数据目录拷贝到从的相应数据目录]。
3、	主DB server开启二进制日志,主DB server和从DB server的server_id都必须唯一。
4、 mysql主从有延迟，是由于原理所致，需要根据业务场景分析是否实现主从，以免造成不可逆的代价。
5、 读写分离解决读数据库的压力，并不能很好的解决写数据库。
步骤：
为主从mysql添加服务
进入D:\mysql\3381\bin目录下执行
```mysql
.\mysqld.exe install MySQL-3380 --defaults-file="D:\mysql\3380\data\my.ini" 
```

1.主库配置，开启二进制日志，用于从库调用进行同步
2.主库为从库授权(创建复制帐号)
	
```mysql
# '定义从库用户名称'@'从库IP' by '该用户访问主库密码'
grant replication slave on *.* to 'slave01'@'127.0.0.1' identified by '123456';
flush privileges;
```

3.从库配置
主库执行获取二进制日志文件名称
使用navicat连接主库执行

```mysql
SHOW MASTER STATUS;
# 结果：
mysql-bin.000002   420	taotao		
```
file mysql-bin.000002
position 420
这两个参数用于配置从库

根据刚刚执行的命令(grant replication slave on *.* to 'slave01'@'127.0.0.1' identified by '123456';)，配置从库
master_host 定义从库用户名称
master_user 从库IP
master_password 该用户访问主库密码
master_port 主库端口(3380)
master_log_file 需要复制的主库中二进制文件名称
master_log_pos 文件位置

从库执行以下命令配置从库
```mysql
CHANGE MASTER TO
 master_host='127.0.0.1',
 master_user='slave01',
 master_password='123456',
 master_port=3380,
 master_log_file='mysql-bin.000002',
 master_log_pos=420;
```
4.启动从库同步，查看从库状态
进入从库

```mysql
START SLAVE;
```
如果启动出错需要删除掉主库从库的日志文件，和配置文件，再次启动

```mysql
SHOW SLAVE STATUS;
# 结果：
	127.0.0.1	slave01	3380	60	mysql-bin.000002	420	PC-201701242302-relay-bin.000001	4	mysql-bin.000002	No	Yes							0		0	420	120	None		0	No							No	1593	Fatal error: The slave I/O thread stops because master and slave have equal MySQL server UUIDs; these UUIDs must be different for replication to work.	0			80		D:\mysql\3381\data\data\master.info	0		Slave has read all relay log; waiting for the slave I/O thread to update it	86400		170304 12:18:46						0
```

查看结果
Slave_IO_Running:No
Slave_SQL_Running:Yes
两个都为Yes说明主从配置成功，需要查看从库日志排除配置失败的原因
D:\mysql\3381\logs\mysql.err.log

```mysql
2017-03-04 12:18:46 7764 [ERROR] Slave I/O: Fatal error: The slave I/O thread stops because master and slave have equal MySQL server UUIDs; these UUIDs must be different for replication to work. Error_code: 1593
```
说明主从数据库uuid相同了，需要从新设置从mysql的uuid
找到从数据库中D:\mysql\3381\data\data\auto.cnf配置文件，内容如下，修改一个值即可

```mysql
[auto]
server-uuid=d71ec2ee-65ba-11e5-a4e3-000c29deb96f
```

重启从数据库服务

```mysql
SHOW SLAVE STATUS;
# 结果
Waiting for master to send event	127.0.0.1	slave01	3380	60	mysql-bin.000002	420	PC-201701242302-relay-bin.000003	283	mysql-bin.000002	Yes	Yes							0		0	420	466	None		0	No						0	No	0		0			80	d71ec2ee-65ba-11e5-a4e3-000c29deb96e	D:\mysql\3381\data\data\master.info	0		Slave has read all relay log; waiting for the slave I/O thread to update it	86400								0
```

Slave_IO_Running:Yes
Slave_SQL_Running:Yes
要重新启动从库同步

```mysql
START SLAVE;
```

不同需要步时执行以下sql停止同步

```mysql
START SLAVE;
```

5.测试主从
修改主数据数据，从数据库跟着该表，MySql主从同步成功。
以下是详细配置文件
主my.ini：

```mysql
[client]
no-beep

# pipe
socket=0.0
port=3380

[mysql]

default-character-set=utf8

# SERVER SECTION
# ----------------------------------------------------------------------
#
# The following options will be read by the MySQL Server. Make sure that
# you have installed the server correctly (see above) so it reads this 
# file.
#
# server_type=3
[mysqld]

enable-named-pipe

shared-memory

shared-memory-base-name=MYSQL-3380

# The Pipe the MySQL Server will use
socket=MYSQL-3380

# The TCP/IP Port the MySQL Server will listen on
port=3380

# Path to installation directory. All paths are usually resolved relative to this.
# basedir="D:/mysql/3380/"

# Path to the database root
datadir=D:/mysql/3380/data/Data

character-set-server=utf8

default-storage-engine=INNODB

sql-mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION"

log-output=FILE
general-log=0
general_log_file="WIN-A1JA6NUDTV4.log"
slow-query-log=1
slow_query_log_file="D:/mysql/3380/logs/mysql-slow.log"
long_query_time=10

# Binary Logging. 主数据库要开启二进制日志
log-bin="D:/mysql/3380/logs/mysql-bin"

# Error Logging.
log-error="D:/mysql/3380/logs/mysql.err.log"

# Server Id.
server-id=80

# 主数据库中配置需要同步的数据库为taotao
binlog-do-db=taotao

max_connections=151

query_cache_size=0

table_open_cache=2000

tmp_table_size=14M

thread_cache_size=10

myisam_max_sort_file_size=100G

myisam_sort_buffer_size=18M

key_buffer_size=8M

read_buffer_size=37K
read_rnd_buffer_size=256K

sort_buffer_size=256K

innodb_additional_mem_pool_size=2M

innodb_flush_log_at_trx_commit=1

innodb_log_buffer_size=1M

innodb_buffer_pool_size=43M

innodb_log_file_size=48M

innodb_thread_concurrency=8

innodb_autoextend_increment=64

innodb_buffer_pool_instances=8

innodb_concurrency_tickets=5000

innodb_old_blocks_time=1000

innodb_open_files=300

innodb_stats_on_metadata=0

innodb_file_per_table=1

innodb_checksum_algorithm=0

back_log=80

flush_time=0

join_buffer_size=256K

max_allowed_packet=4M

max_connect_errors=100

open_files_limit=4161

query_cache_type=0

sort_buffer_size=256K

table_definition_cache=1400

binlog_row_event_max_size=8K

sync_master_info=10000

sync_relay_log=10000

sync_relay_log_info=10000
```
从my.ini
```mysql
[client]
no-beep

# pipe
socket=0.0
port=3381

[mysql]

default-character-set=utf8

# SERVER SECTION
# ----------------------------------------------------------------------
#
# The following options will be read by the MySQL Server. Make sure that
# you have installed the server correctly (see above) so it reads this 
# file.
#
# server_type=3
[mysqld]

# The next three options are mutually exclusive to SERVER_PORT below.
# skip-networking

enable-named-pipe

shared-memory

shared-memory-base-name=MYSQL-3381

# The Pipe the MySQL Server will use
socket=MYSQL-3381

# The TCP/IP Port the MySQL Server will listen on
port=3381

# Path to installation directory. All paths are usually resolved relative to this.
# basedir="D:/mysql/3381/"

# Path to the database root
datadir=D:/mysql/3381/data/data

# The default character set that will be used when a new schema or table is
# created and no character set is defined
character-set-server=utf8

# The default storage engine that will be used when create new tables when
default-storage-engine=INNODB

# Set the SQL mode to strict
sql-mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION"

# Enable Windows Authentication
# plugin-load=authentication_windows.dll

# General and Slow logging.
log-output=FILE
general-log=0
general_log_file="WIN-A1JA6NUDTV4.log"
slow-query-log=1
slow_query_log_file="D:/mysql/3381/logs/mysql-slow.log"
long_query_time=10

# Binary Logging.
log-bin="D:/mysql/3381/logs/mysql-bin"

# Error Logging.
log-error="D:/mysql/3381/logs/mysql.err.log"

# Server Id.
server-id=81

max_connections=151

query_cache_size=0

table_open_cache=2000

tmp_table_size=14M

thread_cache_size=10

myisam_max_sort_file_size=100G

myisam_sort_buffer_size=18M

key_buffer_size=8M

read_buffer_size=37K
read_rnd_buffer_size=256K

sort_buffer_size=256K

innodb_additional_mem_pool_size=2M

innodb_flush_log_at_trx_commit=1

innodb_log_buffer_size=1M

innodb_buffer_pool_size=43M

innodb_log_file_size=48M

innodb_thread_concurrency=8

innodb_autoextend_increment=64

innodb_buffer_pool_instances=8

innodb_concurrency_tickets=5000

innodb_old_blocks_time=1000

innodb_open_files=300

innodb_stats_on_metadata=0

innodb_file_per_table=1

innodb_checksum_algorithm=0

back_log=80

flush_time=0

join_buffer_size=256K

max_allowed_packet=4M

max_connect_errors=100

open_files_limit=4161

query_cache_type=0

sort_buffer_size=256K

table_definition_cache=1400

binlog_row_event_max_size=8K

sync_master_info=10000

sync_relay_log=10000

sync_relay_log_info=10000

```
***

之前的主从配置和使用第三方开源框架实现读写分离

详细实现主从如下

### 2 MySQL主从

这里介绍win环境下MySQL的主从和读写分离，同样Linux操作系统下也是这个原理  

master(IP:192.168.1.15)主服务器mysql
slave(IP:192.168.1.200)从服务器mysql
需要注意的地方
1.数据库主从和读写分离不同服务器上mysql版本尽量保持一致，以免造成不必要的麻烦。
2.不同服务器都要含有需要同步的指定数据库。
3.数据库中已存在的表在同步成功后从数据库中也不会同步，只能同步成功配置同步以后创建的表。

#### 开启log_bin
安装服务  
```
C:\mysql\mysql-5.5.47-win32\bin>mysql -install
```
参考：<a href="https://volc1612.gitee.io/blog/2017/01/05/win%E9%85%8D%E7%BD%AEMySQL/">win配置mysql</a>
<!-- more -->
查看log_bin是否开启
![查看log_bin是否开启](https://volc1612.gitee.io/blog/images/MySQL主从配置和读写分离/1.png)
如果log_bin的值OFF需要在配置文件中开启
找到mysql安装目录下的配置文件my.ini，在[mysqld]追加
```
[mysqld]
# 开启log_bin
log_bin
```
#### 配置master(主数据库)my.ini
```
追加[mysqld]配置
# =============读写===========

log-bin = mysql-bin

log-bin-index = mysql-bin.index

# 服务Id，和slave 的server-id不一样，一般取ip最后一节
server-id = 15

sync_binlog = 1

binlog_format = mixed

# 做主从同步的数据库名
binlog-do-db = test

binlog-ignore-db = mysql

binlog-ignore-db = performance_schema

binlog-ignore-db = information_schema
# =============结束===========
```
#### 关联从数据库，并给予全部权限

```mysql
mysql> grant all privileges on *.* to 'root'@'192.168.1.200' identified by 'root' with grant option;
```

![关联从数据库并给予权限](https://volc1612.gitee.io/blog/images/MySQL主从配置和读写分离/2.png)

'root' 					slave从数据库账户
'192.168.1.200'			slaveIP
'root'					slave登录密码

刷新权限
```
mysql> flush privileges;
```
在从服务器上链接主服务器mysql
![链接master数据库](https://volc1612.gitee.io/blog/images/MySQL主从配置和读写分离/3.png)

#### 获取master状态信息

![获取master状态信息](https://volc1612.gitee.io/blog/images/MySQL主从配置和读写分离/4.png)

记录以下两个字段值，从服务器上mysql配置需要用到
File: mysql-bin.000015
Position: 107

#### 配置slave数据库
配置my.ini
```
[mysqld]
#=========新增=====
# 服务Id，和master 的server-id不一样
server-id = 200
log-bin = mysql-bin
relay-log-index = slave-relay-bin.index
relay-log = slave-relay-bin
sync_master_info = 1
sync_relay_log = 1
sync_relay_log_info = 1
#=========结束======
```
重新启动从服务器上的mysql服务
```
mysql> net stop mysql
mysql> net start mysql
```
确认在从服务器上可以链接本地的mysql后进行以下操作
关闭slave数据库同步，关联master数据库信息
```mysql
mysql> stop slave;
mysql> change master to master_host='192.168.1.15',master_user='root',master_port=3306,master_password='root',master_log_file='mysql-bin.000015',master_log_pos=107;
Query OK, 0 rows affected (0.21 sec)

mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State:
                  Master_Host: 192.168.1.15
                  Master_User: root
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000015
          Read_Master_Log_Pos: 107
               Relay_Log_File: slave-relay-bin.000001
                Relay_Log_Pos: 4
        Relay_Master_Log_File: mysql-bin.000015
             Slave_IO_Running: No
            Slave_SQL_Running: No
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 107
              Relay_Log_Space: 107
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 15
1 row in set (0.00 sec)

mysql> start slave;
Query OK, 0 rows affected (0.00 sec)

mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 192.168.1.15
                  Master_User: root
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000015
          Read_Master_Log_Pos: 107
               Relay_Log_File: slave-relay-bin.000002
                Relay_Log_Pos: 253
        Relay_Master_Log_File: mysql-bin.000015
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 107
              Relay_Log_Space: 409
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 15
1 row in set (0.00 sec)

mysql>
```

注意
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
说明主从配置成功

#### 测试主从  
  
master中test数据库和slave中test数据库进行同步测试。  
在master中test数据库新建test表，刷新slave中test数据库会发现同步成功。  
而master不会同步slave中创建的表，想让master同步slave中创建的表可参考以下方案。
	
	
#### master与slave数据恢复

#### 应用切换到slave  
如果master mysql坏掉了，需要将应用转移到slave段。首先在slave端执行：show master status; 
把输出保存下来，然后再把应用切换过去。保存输出的File和Position值将可以用于以后从slave恢复master。  
 具体从slave恢复master的办法：  
 在master端执行：
CHANGE MASTER to MASTER_HOST='192.168.0.203', MASTER_PORT=3306, MASTER_USER='repl', MASTER_PASSWORD='repl', 
MASTER_LOG_FILE='mysql-bin.000006', MASTER_LOG_POS=98 ;
其中 MASTER_HOST 为slave的ip, MASTER_LOG_FILE, MASTER_LOG_POS为切换到slave时记录下的File和Position值。
在master执行：start slave;
待master内容基本恢复以后（在master执行：show slave status \G; Slave_IO_State显示为Waiting for master to send event）
将应用切换回master。
待系统稳定后master执行：stop slave停止slave。如果要避免master重启以后自动开启slave，可以将master 
mysql的data文件夹的master.info文件删掉。
参考：http://blog.sina.com.cn/s/blog_620adf7f0100kj5e.html  

### 使用Amoeba(阿米巴)开源框架实现mysql读写分离(负载均衡)

### 准备
环境:
	windows 64
	amoeba-for-mysql-2.2.0.zip
	java version "1.8.0_31"
	mysql-5.5.47-win32
win需要安装jdk运行环境支持amoeba的运行
mastermysqlIP:192.168.1.15
slavemysqlIP:192.168.1.200
amoeba-for-mysql-2安装在IP:192.168.1.15
master和slave已实现了主从，配置主从可参考上面
下面介绍一下amoeba的使用
### 配置amoeba

将下载好的amoeba-for-mysql-2.2.0.zip文件解压到C:\mysql\amoeba-for-mysql-2.2.0
![目录结构](https://volc1612.gitee.io/blog/images/MySQL主从配置和读写分离/9.png)

conf目录下有dbServers.xml和amoeba.xml两个文件需要修改
dbServers.xml
```
<?xml version="1.0" encoding="gbk"?>

<!DOCTYPE amoeba:dbServers SYSTEM "dbserver.dtd">
<amoeba:dbServers xmlns:amoeba="http://amoeba.meidusa.com/">

		<!-- 
			Each dbServer needs to be configured into a Pool,
			If you need to configure multiple dbServer with load balancing that can be simplified by the following configuration:
			 add attribute with name virtual = "true" in dbServer, but the configuration does not allow the element with name factoryConfig
			 such as 'multiPool' dbServer   
		-->
		
	<dbServer name="abstractServer" abstractive="true">
		<factoryConfig class="com.meidusa.amoeba.mysql.net.MysqlServerConnectionFactory">
			<property name="manager">${defaultManager}</property>
			<property name="sendBufferSize">64</property>
			<property name="receiveBufferSize">128</property>
			
			<!-- amoeba账户信息，主从服务器上的mysql需要为amoeba账号授权 -->
			<!-- mysql port master数据库端口号-->
			<property name="port">3306</property>
			
			<!-- mysql schema master读写分离的数据库名称-->
			<property name="schema">newchange</property>
			
			<!-- mysql user amoeba账号名称-->
			<property name="user">amoeba</property>
			
			<!-- amoeba账号密码 -->
			<property name="password">123456</property>
			
		</factoryConfig>

		<poolConfig class="com.meidusa.amoeba.net.poolable.PoolableObjectPool">
			<property name="maxActive">500</property>
			<property name="maxIdle">500</property>
			<property name="minIdle">10</property>
			<property name="minEvictableIdleTimeMillis">600000</property>
			<property name="timeBetweenEvictionRunsMillis">600000</property>
			<property name="testOnBorrow">true</property>
			<property name="testOnReturn">true</property>
			<property name="testWhileIdle">true</property>
		</poolConfig>
	</dbServer>
	<!-- mastermysql -->
	<dbServer name="master"  parent="abstractServer">
		<factoryConfig>
			<!-- mysql ip -->
			<property name="ipAddress">192.168.1.15</property>
		</factoryConfig>
	</dbServer>
	<!-- slavemysql -->
	<dbServer name="slave"  parent="abstractServer">
		<factoryConfig>
			<!-- mysql ip -->
			<property name="ipAddress">192.168.1.200</property>
		</factoryConfig>
	</dbServer>
	
	<dbServer name="multiPool" virtual="true">
		<poolConfig class="com.meidusa.amoeba.server.MultipleServerPool">
			<!-- Load balancing strategy: 1=ROUNDROBIN , 2=WEIGHTBASED , 3=HA-->
			<!-- 1为轮询，2为并 -->
			<property name="loadbalance">1</property>
			<!-- Separated by commas,such as: server1,server2,server1 -->
			<property name="poolNames">master,slave</property>
		</poolConfig>
	</dbServer>
		
</amoeba:dbServers>
```
amoeba.xml
```
<?xml version="1.0" encoding="gbk"?>

<!DOCTYPE amoeba:configuration SYSTEM "amoeba.dtd">
<amoeba:configuration xmlns:amoeba="http://amoeba.meidusa.com/">

	<proxy>
	
		<!-- service class must implements com.meidusa.amoeba.service.Service -->
		<service name="Amoeba for Mysql" class="com.meidusa.amoeba.net.ServerableConnectionManager">
			<!-- port amoeba账号访问端口 -->
			<property name="port">8066</property>
			
			<!-- bind ipAddress -->
			
			<property name="ipAddress">127.0.0.1</property>
			
			
			<property name="manager">${clientConnectioneManager}</property>
			
			<property name="connectionFactory">
				<bean class="com.meidusa.amoeba.mysql.net.MysqlClientConnectionFactory">
					<property name="sendBufferSize">128</property>
					<property name="receiveBufferSize">64</property>
				</bean>
			</property>
			
			<property name="authenticator">
				<bean class="com.meidusa.amoeba.mysql.server.MysqlClientAuthenticator">
					
					<!-- mysql客户端访问amoeba账号配置 -->
					<property name="user">amoebaroot</property>
					<property name="password">123456</property>
					<property name="filter">
						<bean class="com.meidusa.amoeba.server.IPAccessController">
							<property name="ipFile">${amoeba.home}/conf/access_list.conf</property>
						</bean>
					</property>
				</bean>
			</property>
			
		</service>
		
		<!-- server class must implements com.meidusa.amoeba.service.Service -->
		<service name="Amoeba Monitor Server" class="com.meidusa.amoeba.monitor.MonitorServer">
			<!-- port -->
			<!--  default value: random number
			<property name="port">9066</property>
			-->
			<!-- bind ipAddress -->
			<property name="ipAddress">127.0.0.1</property>
			<property name="daemon">true</property>
			<property name="manager">${clientConnectioneManager}</property>
			<property name="connectionFactory">
				<bean class="com.meidusa.amoeba.monitor.net.MonitorClientConnectionFactory"></bean>
			</property>
			
		</service>
		
		<runtime class="com.meidusa.amoeba.mysql.context.MysqlRuntimeContext">
			<!-- proxy server net IO Read thread size -->
			<property name="readThreadPoolSize">20</property>
			
			<!-- proxy server client process thread size -->
			<property name="clientSideThreadPoolSize">30</property>
			
			<!-- mysql server data packet process thread size -->
			<property name="serverSideThreadPoolSize">30</property>
			
			<!-- per connection cache prepared statement size  -->
			<property name="statementCacheSize">500</property>
			
			<!-- query timeout( default: 60 second , TimeUnit:second) -->
			<property name="queryTimeout">60</property>
		</runtime>
		
	</proxy>
	
	<!-- 
		Each ConnectionManager will start as thread
		manager responsible for the Connection IO read , Death Detection
	-->
	<connectionManagerList>
		<connectionManager name="clientConnectioneManager" class="com.meidusa.amoeba.net.MultiConnectionManagerWrapper">
			<property name="subManagerClassName">com.meidusa.amoeba.net.ConnectionManager</property>
			<!-- 
			  default value is avaliable Processors 
			<property name="processors">5</property>
			 -->
		</connectionManager>
		<connectionManager name="defaultManager" class="com.meidusa.amoeba.net.MultiConnectionManagerWrapper">
			<property name="subManagerClassName">com.meidusa.amoeba.net.AuthingableConnectionManager</property>
			
			<!-- 
			  default value is avaliable Processors 
			<property name="processors">5</property>
			 -->
		</connectionManager>
	</connectionManagerList>
	
		<!-- default using file loader -->
	<dbServerLoader class="com.meidusa.amoeba.context.DBServerConfigFileLoader">
		<property name="configFile">${amoeba.home}/conf/dbServers.xml</property>
	</dbServerLoader>
	
	<queryRouter class="com.meidusa.amoeba.mysql.parser.MysqlQueryRouter">
		<property name="ruleLoader">
			<bean class="com.meidusa.amoeba.route.TableRuleFileLoader">
				<property name="ruleFile">${amoeba.home}/conf/rule.xml</property>
				<property name="functionFile">${amoeba.home}/conf/ruleFunctionMap.xml</property>
			</bean>
		</property>
		<property name="sqlFunctionFile">${amoeba.home}/conf/functionMap.xml</property>
		<property name="LRUMapSize">1500</property>
		<property name="defaultPool">master</property>
		<!-- 可写数据库 -->
		<property name="writePool">master</property>
		<!-- 可读的数据库 -->
		<property name="readPool">multiPool</property>	
		<property name="needParse">true</property>
	</queryRouter>
</amoeba:configuration>
```
配置文件配置完成，主从mysql为amoeba授权

分别登录主从mysql执行命令
```mysql
mysql> grant all on newchange.* to amoeba@'192.168.1.%' identified by '123456';
flush privileges;
```
启动amoeba
```
C:\mysql\amoeba-for-mysql-2.2.0\bin>amoeba
amoeba start|stop
请按任意键继续. . .

C:\mysql\amoeba-for-mysql-2.2.0\bin>amoeba start
log4j:WARN log4j config load completed from file:C:\mysql\amoeba-for-mysql-2.2.0
\bin\..\conf\log4j.xml
2017-01-06 14:46:42,470 INFO  context.MysqlRuntimeContext - Amoeba for Mysql cur
rent versoin=5.1.45-mysql-amoeba-proxy-2.2.0
log4j:WARN ip access config load completed from file:C:\mysql\amoeba-for-mysql-2
.2.0\bin\../conf/access_list.conf
2017-01-06 14:46:42,645 INFO  net.ServerableConnectionManager - Amoeba for Mysql
 listening on /127.0.0.1:8066.
2017-01-06 14:46:42,648 INFO  net.ServerableConnectionManager - Amoeba Monitor S
erver listening on /127.0.0.1:49986.

```

#### 测试读写分离及负载均衡

在登录master主库上newchange数据库中创建liu表
```mysql
mysql> create table liu (id int(10), text varchar(20));
mysql> insert into liu values('1', 'this_is_master');
```
登录slave从库停掉从库复制进程
```mysql
mysql> stop slave;
mysql> insert into liu values('2',,'this_is_slave');
```
192.168.1.15(master服务器)连接amoeba客户端
![链接amoeba](https://volc1612.gitee.io/blog/images/MySQL主从配置和读写分离/10.png)


```
C:\mysql\mysql-5.5.47-win32\bin>mysql -u amoebaroot -p -h 127.0.0.1 -P 8066
Enter password: ******
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1164729376
Server version: 5.1.45-mysql-amoeba-proxy-2.2.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use newchange;
Database changed
```
因为默认连的数据库是主库，第一次查询查的是主库的数据
```mysql
mysql> select * from liu;
+------+----------------+
| id   | text           |
+------+----------------+
|  1   | this_is_master |
+------+----------------+
1 rows in set (0.00 sec)
```
因为开启了负载均衡并采用轮询模式该查询查的是从库的数据
```mysql
mysql> select * from liu;
+------+---------------+
| id   | text          |
+------+---------------+
|  1   | this_is_master|
|  2   | this_is_slave |
+------+---------------+
2 row in set (0.00 sec)
```
插入两条数据，观察数据插入到哪个库中

```mysql
mysql> insert into liu values('3','this_is_master');
Query OK, 1 row affected (0.00 sec)

mysql> insert into liu values('3','this_is_master');
Query OK, 1 row affected (0.01 sec)
```
在主库查询
```mysql
mysql> select * from liu;
+------+----------------+
| id   | text           |
+------+----------------+
|  1   | this_is_master |
|  3   | this_is_master |
|  4   | this_is_master |
+------+----------------+
3 rows in set (0.00 sec)
```
在从库查询
```mysql
mysql> select * from liu;
+------+---------------+
| id   |   text        |
+------+---------------+
|  2   | this_is_slave |
+------+---------------+
1 row in set (0.00 sec)
```
可见数据插入到了主库上，因为配置了主库可读写、从库为只读，且主从同步中从库的同步进程已被停用。

配置参考：http://docs.hexnova.com/amoeba/single-dbserver.html#example.dbServer.abstractServer.port
