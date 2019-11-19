---
title: linux常用命令
date: 2016-11-23 16:11:36
update: 2016-12-25 16:11:36
categories: Linux
tags: [linux]
---

### LINUX常用命令

> ls命令   

```apache
ls -l                                             # 目录详细结构
```
<!-- more -->                                        
> mkdir命令                                         

```apache
mkdir folder1                                     # 创建folder1目录
```
                                      
> rm命令                                            

```apache
[root@VM_0_11_redhat lc]# rm -rf /var/log/httpd/access 						# 删除access目录以及access下的子目录
[root@VM_0_11_redhat lc]# rm file                                           # 删除lc目录下的file文件
```
复制dir1下的所有文件包含隐藏文件复制到dir2中
dir2不存在：
```
[root@VM_0_11_redhat lc]# cp -r dir1 dir2
```
dir2不存在

```
[root@VM_0_11_redhat lc]# cp -r dir1/. dir2
```

> cp命令                                            

```
[root@VM_0_11_redhat lc]# cp file1 file2                                    # 复制file1文件到file2
[root@VM_0_11_redhat lc]# cp -r file2                                    # 复制file1文件到file2
```
                                                  
> mv命令                                            

```apache
[root@VM_0_11_redhat lc]# mv file1.txt file2.txt                            # 重命名
[root@VM_0_11_redhat lc]# mv folder1 folder2                                # 移动folder1文件夹(包含folder1)到folder2文件夹
[root@VM_0_11_redhat lc]# mv mysql-8.0.3-rc-linux-glibc2.12-x86_64.tar ../mysql # 移动文件到该文件所在目录(lc)的同级目录(mysql)中
[root@VM_0_11_redhat mysql]# mv mysql-8.0/* /home/mysql/mysql               # 移动mysql-8.0下的所有文件到/home/mysql/mysql目录下

```
> tar 解压
解压到当前目录
```
[root@VM_0_11_redhat lc]# tar -xvf apache-tomcat-7.0.57.tar.gz
```

解压到/home/mysql文件夹下
```
[root@VM_0_11_redhat lc]# tar -zxf mysql-5.7.12-linux-glibc2.5-x86_64.tar.gz -C /home/mysql
```
                                         
> vi命令  

```apache
vi file1.txt                                      # 查看file1.txt文件第一页(command mode),trl + f进行翻页
                                                  # 按i建进入编辑模式(insert mode)
				                  # 退出vi命令:
				                  # Esc按键(command mode) 输入':q'回车退出
				                  # ':wq'保存退出
				                  # ':q!'强制退出
				                  # 输入':/str'回车查找str字符串，按n查看下一个
```

> cat命令  

```apache
cat file1.txt                                     # 查看file1.txt所有信息
cat file1.txt | grep 'info'                       # 查看file1.txt中匹配'info'所在行信息,并标记'info'字符串
cat file1 file2 > file                            # file1和file2合并到file问价并显示
cat must.txt | tail -n +10 | head -n 10           # 查看must.txt文件，从第10行起显示10行，即'10-19'行
cat >> filename 								# 创建名字为filename的文件，编写完成后按Ctrl + d 退出并保存
cat -n logs/error.log | grep "debug" | more      # 查找debug字段所在页的数据，并按空格键翻页
```


                                                 
> tail命令                                          

```apache
tailf catalina.out | grep 'countTime'             # 只实时显示含有'countTime'所在行的信息
tail -f file1                                     # 实时显示file1文件后十条并进入编辑模式
tail -n 200 -f file1                              # 实时显示file1后200行变化信息，和上面的命令效果相似	
tail -n 200 file1 | grep 'test'                   # 查看file1文件后200行，高亮'test'(最常用)
tail -n 200 -f file1 | grep 'INFO*'               # 实时显示file1后200行变化信息，并标记处'INFO'字符串(最常用)
```
                                         
> find命令                                          

```apache
find folder1 -type f -name "*.txt"                # 查询当前目录下的folder1文件夹中匹配'*.txt'的文件或文件夹
find folder1 -name 'must.txt' -ls                 # 查询当前目录下的folder1文件夹中匹配'*.txt'的文件或文件夹，并显示信息
find -type f -name 'must.txt'|xargs grep 'str'    # 查询当前目录下的must.txt文件中匹配'str'所在行的信息
find / -name "memca*"							  # 根据文件名称全局查找
grep -rn "str" *                                  # 查询当前目录下的must.txt文件中匹配'str'所在行的信息
    # * : 表示当前目录所有文件，也可以是某个文件名
    # -r 是递归查找
    # -n 是显示行号
    # -R 查找所有文件包含子目录
    # -i 忽略大小写
```

> echo命令

```apache
echo "hello world" > file.txt                     # 当前目录下创建file.txt文件并添加数据
```

### 查看服务器外网ip
```apache
	curl http://members.3322.org/dyndns/getip 
```
	
### 查看服务器网络地址

```apache
[root@iZbp199af3f3y8j7fr8kolZ ~]# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.27.235.142  netmask 255.255.252.0  broadcast 10.27.235.255
        ether 00:16:3e:0d:f7:47  txqueuelen 1000  (Ethernet)
        RX packets 15622862  bytes 1152767495 (1.0 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 107645  bytes 7704353 (7.3 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 116.62.29.229  netmask 255.255.252.0  broadcast 116.62.31.255
        ether 00:16:3e:0c:a6:a1  txqueuelen 1000  (Ethernet)
        RX packets 261064410  bytes 18335028955 (17.0 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 5924776  bytes 1812912938 (1.6 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 0  (Local Loopback)
        RX packets 18419107  bytes 2800651279 (2.6 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 18419107  bytes 2800651279 (2.6 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

```
其中eth0为内网，eth1为外网

### 更改文件或文件夹权限

改变一个文件的权限: chmod mode file|dir
改变所有子目录的权限: chmod mode dir -R

mode = 777 or 752 and so on.
mode的三个数字，分别表示 owner，group,others所具有的权限。
1 = x 执行 2 = w 写 4 = r 读，比如owner具有所有权限，1+2+4=7,
又比如 group 具有 读 和 执行 权限 1+4 = 5

### 查看端口使用情况  
查看正在使用的端口号  

```apache
	netstat -anp
	tcp        0      0 114.55.113.160:50358    106.11.68.13:80 ESTABLISHED 1338/AliYunDun
```
### 查看端口号对应的服务名称  

```apache
	lsof -i:"50358"
	AliYunDun 1338 root   11u  IPv4 7340009      0t0  TCP 114.55.113.160:50358->106.11.68.13:http (ESTABLISHED)
```
### 根据pid查看进程信息所在目录  

```apache
	lsof -p "24619"
```
### 根据PID进程号关闭服务  

```apache
	kill -9 "1338"
```
### 根据服务名称查看是否启用   

```apache
	ps -aux | grep "memcache"
	root     25899  0.0  0.0 112648   972 pts/0    S+   11:16   0:00 grep --color=auto memcache
```

### 查询端口号对应的pID或pid对应的端口号

```
[root@localhost ssp-service-1.0.3-SNAPSHOT]# netstat -lntp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name       
tcp6       0      0 :::4035(端口号)                 :::*                    LISTEN      19663(进程号)/java(程序名)
```

### 通过Pid或服务名称查询属于哪个服务

```
[root@localhost ssp-service-1.0.3-SNAPSHOT]# ps -aux | grep 19663
USER PID %CUP %MEM VSZ RSS TTY STAT START TIME COMMAND
root(user)     19663(pid)  6.4(cup)  1.7(mem) 5553636(vsz) 1092952(rss) ?(tty)     Sl(stat)   13:42(启动时间)   2:00(time) java -Dspring.profiles.active=test -server -Xms512m -Xmx512m -XX:+UseG1GC -XX:MaxGCPauseMillis=200 -verbose:gc -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -Xloggc:/data/logs/ssp-service-1.0.3-SNAPSHOT/ssp-service-1.0.3-SNAPSHOT-gc.log -jar ./ssp-service-1.0.3-SNAPSHOT.jar(command)
root     22796  0.0  0.0 112712   944 pts/1    S+   14:13   0:00 grep --color 19663
```

### ./和sh执行脚本的区别  
./需要执行权限  
sh不需要，sh是个软链接，它可能是一个任意的shell，通常默认是bash shell  
用type命令可以查看  
./执行调用的是你开头一行定义的shell，不指定系统会调用默认shell程序  


### 查看linux版本信息
```
[root@VM_0_11_redhat /]# cat /proc/version
Linux version 3.10.0-514.21.1.el7.x86_64 (builder@kbuilder.dev.centos.org) (gcc version 4.8.5 20150623 (Red Hat 4.8.5-11) (GCC) ) #1 SMP Thu May 25 17:04:51 UTC 2017
```
查看所有linux版本信息
```
[root@VM_0_11_redhat /]# yum install redhat-lsb -y
[root@VM_0_11_redhat /]# lsb_release -a
LSB Version:	:core-4.1-amd64:core-4.1-noarch:cxx-4.1-amd64:cxx-4.1-noarch:desktop-4.1-amd64:desktop-4.1-noarch:languages-4.1-amd64:languages-4.1-noarch:printing-4.1-amd64:printing-4.1-noarch
Distributor ID:	CentOS
Description:	CentOS Linux release 7.3.1611 (Core) 
Release:	7.3.1611
Codename:	Core
[root@VM_0_11_redhat /]# 

```
查看cpu信息
```
[root@VM_0_11_redhat ~]# cat /proc/cpuinfo
processor	: 0
vendor_id	: GenuineIntel
cpu family	: 6
model		: 79
model name	: Intel(R) Xeon(R) CPU E5-26xx v4
stepping	: 1
microcode	: 0x1
cpu MHz		: 2394.454
cache size	: 4096 KB
physical id	: 0
siblings	: 1
core id		: 0
cpu cores	: 1
apicid		: 0
initial apicid	: 0
fpu		: yes
fpu_exception	: yes
cpuid level	: 13
wp		: yes
flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ss ht syscall nx lm constant_tsc rep_good nopl eagerfpu pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand hypervisor lahf_lm abm 3dnowprefetch bmi1 avx2 bmi2 rdseed adx xsaveopt
bogomips	: 4788.90
clflush size	: 64
cache_alignment	: 64
address sizes	: 40 bits physical, 48 bits virtual
power management:
```


### 组操作

#### 查询所有组信息

```
[root@VM_0_11_redhat ~]# cat /etc/group

```

#### 查询组下所有用户

mysql为组名

```
[root@VM_0_11_redhat ~]# grep 'mysql' /etc/group
mysql:x:1002:
[root@VM_0_11_redhat ~]# awk -F":" '{print $1"/t/t"$4}' /etc/passwd | grep '1000'
zhang/t/t1000
[root@VM_0_11_redhat ~]# awk -F":" '{print $1"/t/t"$4}' /etc/passwd | grep '1002'
```

#### 创建组和删除组

##### 创建组

```
[root@VM_0_11_redhat ~]# groupadd mysql
```

##### 在mysql组创建用户lcmysql

```
[root@VM_0_11_redhat mysql-8.0]# useradd -g mysql -d /home/mysql/mysql-8.0 lcmysql
useradd: warning: the home directory already exists.
Not copying any file from skel directory into it.
```
useradd -g mysql -d /home/mysql/mysql-8.0 lcmysql 含义:

在mysql组中添加lcmysql用户，并指定lcmysql用户登录时的初始目录为/home/mysql/mysql-8.0

##### 用户授权

将/home/mysql/mysql-8.0 lcmysql目录及其子目录和文件，所有者改变为lcmysql,所属组改变为mysql
```
[root@VM_0_11_redhat mysql-8.0]# chown -R lcmysql:mysql /home/mysql/mysql-8.0 lcmysql
```

##### 修改目录权限

设置/home/mysql/mysql-8.0目录的权限

```
[root@VM_0_11_redhat mysql-8.0]# chmod 755 /home/mysql/mysql-8.0
```

##### 问题

为mysql组中添加lcmysql用户时出现问题

原因：
 
系统添加用户的标准步骤
1.编辑/etc/passwd与/etc/group
2.创建用户主目录
3.从/etc/skel拷贝文件与目录
4.让新用户获得其主目录与文件的拥有权限
5.给新用户一个密码
 
解决办法：
依旧使用上面的脚本建用户，然后手动拷贝配置文件到/u01/oracle下。
 
cp /etc/skel/.bash_profile /home/mysql/mysql-8.0
 
cp /etc/skel/.bashrc /home/mysql/mysql-8.0
 
cp /etc/skel/.bash_logout /home/mysql/mysql-8.0
 
这样既可。

```
[root@VM_0_11_redhat mysql-8.0]# cp /etc/skel/.bash_profile /home/mysql/mysql-8.0
[root@VM_0_11_redhat mysql-8.0]# cp /etc/skel/.bashrc /home/mysql/mysql-8.0
[root@VM_0_11_redhat mysql-8.0]# cp /etc/skel/.bash_logout /home/mysql/mysql-8.0
[root@VM_0_11_redhat mysql-8.0]# su - lcmysql
Last login: Wed Apr 11 23:26:52 CST 2018 on pts/2
[lcmysql@VM_0_11_redhat ~]$ 
```

##### 删除组

删除组之前需要里面的用户
groupdel 组名称
```
[root@VM_0_11_redhat mysql-8.0]# groupdel mysql
```

### 用户操作
#### 查询所有用户的根目录信息

```
[root@VM_0_11_redhat etc]# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-bus-proxy:x:999:997:systemd Bus Proxy:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:998:996:User for polkitd:/:/sbin/nologin
libstoragemgmt:x:997:995:daemon account for libstoragemgmt:/var/run/lsm:/sbin/nologin
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
tss:x:59:59:Account used by the trousers package to sandbox the tcsd daemon:/dev/null:/sbin/nologin
ntp:x:38:38::/etc/ntp:/sbin/nologin
abrt:x:173:173::/etc/abrt:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
chrony:x:996:994::/var/lib/chrony:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
zhang:x:1000:1000::/home/zhang:/bin/bash
lc:x:1001:1001::/home/lc:/bin/bash
redis:x:995:993:Redis Database Server:/var/lib/redis:/sbin/nologin
mysql:x:27:27:MySQL Server:/var/lib/mysql:/bin/false
ftplc:x:1002:1002::/home/ftplc:/sbin/nologin
```


#### 创建用户

##### 创建用户
使用root(超级管理员)创建lc(普通用户)
```
[root@VM_0_11_redhat ~]# useradd lc
```
##### 设置密码

root用户登录，设置lc用户的密码
```
[root@VM_0_11_redhat ~]# passwd lc
Changing password for user lc.
New password: 
BAD PASSWORD: The password is shorter than 8 characters
Retype new password: 
passwd: all authentication tokens updated successfully.
```
切换用户时出现问题

```
[lc@VM_0_11_redhat ~]$ su - lcmysql
Password: 
Last login: Thu Mar 29 00:47:24 CST 2018 on pts/0
-bash-4.2$ 
```
退出状态码
exit nnn
```
-bash-4.2$ exit
logout
-bash: exit: nnn: numeric argument required
[root@VM_0_11_redhat mysql-8.0]# 
```

出现-bash-4.2$状态原因：

在linux下通过useradd方式创建新用户时，都会将所有的配置文件从/etc/skel复制到/home目录的新用户录下。但现在这个mysql目录是新建立的，空的，那么就复制/etc/skel这个目录的文件到/home/mysql中：
执行命令：

```
[root@VM_0_11_redhat ~]# cp -r /etc/skel/. /home/mysql
[root@VM_0_11_redhat mysql]# ls -a
.  ..  .bash_logout  .bash_profile  .bashrc
[root@VM_0_11_redhat mysql]# su - lcmysql
Last login: Thu Mar 29 00:53:50 CST 2018 on pts/0
[lcmysql@VM_0_11_redhat ~]$ 
```

#### 删除用户

删除ftplc用户同时删除用户的家目录和邮件通知目录

```
[root@VM_0_11_redhat ftp]# userdel -r ftplc
userdel: user ftplc is currently used by process 5343
```
杀掉进程号再删除


### 杀掉进程

kill -9 进程号

```
[root@VM_0_11_redhat mysql-8.0]# kill -9 5910
[root@VM_0_11_redhat mysql-8.0]# Killed
```

### 查找目录
```
# 查找tomcat目录
[root@VM_0_11_redhat ~]# find / -name tomcat
/usr/local/qcloud/YunJing/VulnerDetect/yhvs/lib/utils/app/tomcat
/etc/selinux/targeted/active/modules/100/tomcat
[root@VM_0_11_redhat ~]# 

```

### 查看linux是否安装tomcat
```
[root@VM_0_11_redhat bin]# rpm -qa|grep tomcat
[root@VM_0_11_redhat bin]# 

```

### 查找软件是否启动

```
[root@VM_0_11_redhat /]# ps -ef | grep redis
redis      451     1  0 Feb20 ?        00:45:24 /usr/bin/redis-server *:6379
root     17400 17250  0 22:45 pts/0    00:00:00 grep --color=auto redis
```
ps -ef | grep 程序名称

### vim命令

```
[root@VM_0_11_redhat logs]#  vim 123.txt
```
按 i 进行编辑操作
按 esc 退出编辑，再按u或者ctrl + r 撤销编辑

### 查看jdk安装路径
```
[root@VM_0_11_redhat /]# which java
/usr/bin/java
[root@VM_0_11_redhat /]# ls -lrt /usr/bin/java
lrwxrwxrwx 1 root root 22 Feb  9 10:19 /usr/bin/java -> /etc/alternatives/java
[root@VM_0_11_redhat /]# ls -lrt /etc/alternatives/java
lrwxrwxrwx 1 root root 73 Feb  9 10:19 /etc/alternatives/java -> /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.161-0.b14.el7_4.x86_64/jre/bin/java
[root@VM_0_11_redhat /]# 
```
which java : 是定位不到安装路径的。which java定位到的是java程序的执行路径(定位到java程序的执行路径是/usr/bin/java)
ls -lrt /usr/bin/java ： 再次查找/usr/bin/java的执行路径是/etc/alternatives/java，以此类推查找到jdk的安装路径


### ps -aux | grep xxx命令讲解

该命令可查看是否安装了xxx程序，并查看程序状态
```
[root@VM_0_11_redhat home]# ps -aux | grep mysql
root     22318  0.0  0.0 112652   968 pts/0    R+   00:26   0:00 grep --color=auto mysql
[root@VM_0_11_redhat home]# ps -aux | grep java
root     15826  0.1 10.3 2335932 105296 ?      Sl   Mar25   3:37 /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.161-0.b14.el7_4.x86_64/jre/bin/java -Djava.util.logging.config.file=/home/lc/tomcat9-test/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dignore.endorsed.dirs= -classpath /home/lc/tomcat9-test/bin/bootstrap.jar:/home/lc/tomcat9-test/bin/tomcat-juli.jar -Dcatalina.base=/home/lc/tomcat9-test -Dcatalina.home=/home/lc/tomcat9-test -Djava.io.tmpdir=/home/lc/tomcat9-test/temp org.apache.catalina.startup.Bootstrap start
root     22322  0.0  0.0 112652   968 pts/0    R+   00:26   0:00 grep --color=auto java
```
ps aux输出格式：
USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
说明：
USER: 行程拥有者
PID: pid
%CPU: 占用的 CPU 使用率
%MEM: 占用的记忆体使用率
VSZ: 占用的虚拟记忆体大小
RSS: 占用的记忆体大小
TTY: 终端的次要装置号码 (minor device number of tty)
STAT: 该行程的状态，linux的进程有5种状态：

```
	D 不可中断 uninterruptible sleep (usually IO)
	R 运行 runnable (on run queue)
	S 中断 sleeping，包含子进程
	T 停止 traced or stopped
	Z 僵死 a defunct (”zombie”) process
	其他状态：
	 W     进入内存交换（从内核2.6开始无效）
	 X     死掉的进程
     <     高优先级
     N     低优先级
     L     有些页被锁进内存
	 s     包含子进程
     +     位于后台的进程组
     l     多线程，克隆线程   multi-threaded (using CLONE_THREAD, like NPTL pthreads do)
```
	注: 状态W(无驻留页), <(高优先级进程), N(低优先级进程), L(内存锁页).
START: 行程开始时间
TIME: 执行的时间
COMMAND:所执行的指令

### chown命令
格式 ： chown[选项]...[所有者][:[组]]文件...
命令功能：
通过chown改变文件的拥有者和群组。在更改文件的所有者或所属群组时，可以使用用户名称和用户识别码设置。普通用户不能将自己的文件改变成其他的拥有者。其操作权限一般为管理员。
命令参数：
　　必要参数:
　　　　-c显示更改的部分的信息
　　　　-f忽略错误信息
　　　　-h修复符号链接
　　　　-R处理指定目录以及其子目录下的所有文件
　　　　-v显示详细的处理信息
　　　　-deference作用于符号链接的指向，而不是链接文件本身
　　选择参数:
　　　　--reference=<目录或文件>把指定的目录/文件作为参考，把操作的文件/目录设置成参考文件/目录相同拥有者和群组
　　　　--from=<当前用户：当前群组>只有当前用户和群组跟指定的用户和群组相同时才进行改变
　　　　--help显示帮助信息
　　　　--version显示版本信息
例如：
chown -R root:mysql /var/data
将/var/data目录以及子目录的所有者变为root用户，所属组变为mysql

### chmod命令

755 rwxr-xr-x
7：rwx (4+2+1 | 读取+写入+执行) 	User
5：r-x (4+1 | 读取+执行) 			Group
5：r-x (4+1 | 读取+执行)			Other

语法为：chmod abc file
其中a,b,c各为一个数字，分别表示User、Group、及Other的权限。

### 查看linux服务器内存使用情况命令

top命令可以实时动态地查看系统的整体运行情况，是一个综合了多方信息监测系统性能和运行信息的实用工具。通过top命令所提供的互动式界面，用热键可以管理。

```
[root@VM_0_11_redhat home]# top

```

```
[root@VM_0_11_redhat ~]# free
              total        used        free      shared  buff/cache   available
Mem:        1016516      377004       74788         288      564724      458088
Swap:             0           0           0
[root@VM_0_11_redhat ~]# 

```

查看RAM使用情况最简单的方法是通过/proc/meminfo

```
[root@VM_0_11_redhat ~]# cat /proc/meminfo
MemTotal:        1016516 kB
MemFree:           76552 kB
MemAvailable:     460296 kB
Buffers:           99964 kB
Cached:           368936 kB
SwapCached:            0 kB
Active:           585040 kB
Inactive:         232760 kB
Active(anon):     349044 kB
Inactive(anon):      144 kB
Active(file):     235996 kB
```


### 查看linux服务器相关命令

#### 型号
```
[root@ip-172-31-40-223 ~]# dmidecode|grep "System Information" -A9|egrep  "Manufacturer|Product"
    Manufacturer: Xen
    Product Name: HVM domU
[root@ip-172-31-40-223 ~]# 

```

#### 查看linux系统信息命令

```
# uname -a               # 查看内核/操作系统/CPU信息
# head -n 1 /etc/issue   # 查看操作系统版本
# cat /proc/version      # 查看内核版本命令
# cat /proc/cpuinfo      # 查看CPU信息
# hostname               # 查看计算机名
# lspci -tv              # 列出所有PCI设备
# lsusb -tv              # 列出所有USB设备
# lsmod                  # 列出加载的内核模块
# env                    # 查看环境变量
```

#### 资源

```
# free -m                # 查看内存使用量和交换区使用量
# df -h                  # 查看各分区使用情况
# du -sh <目录名>        # 查看指定目录的大小
# grep MemTotal /proc/meminfo   # 查看内存总量
# grep MemFree /proc/meminfo    # 查看空闲内存量
# uptime                 # 查看系统运行时间、用户数、负载
# cat /proc/loadavg      # 查看系统负载
```

#### 磁盘和分区

```
# mount | column -t      # 查看挂接的分区状态
# fdisk -l               # 查看所有分区
# swapon -s              # 查看所有交换分区
# hdparm -i /dev/hda     # 查看磁盘参数(仅适用于IDE设备)
# dmesg | grep IDE       # 查看启动时IDE设备检测状况
```

#### 网络

```
# ifconfig               # 查看所有网络接口的属性
# iptables -L            # 查看防火墙设置
# route -n               # 查看路由表
# netstat -lntp          # 查看所有监听端口
# netstat -antp          # 查看所有已经建立的连接
# netstat -s             # 查看网络统计信息
```

#### 进程

```
# ps -ef                 # 查看所有进程
# top                    # 实时显示进程状态
```

#### 用户

```
# w                      # 查看活动用户
# id <用户名>            # 查看指定用户信息
# last                   # 查看用户登录日志
# cut -d: -f1 /etc/passwd   # 查看系统所有用户
# cut -d: -f1 /etc/group    # 查看系统所有组
# crontab -l             # 查看当前用户的计划任务
```

#### 服务

```
# chkconfig --list       # 列出所有系统服务
# chkconfig --list | grep on    # 列出所有启动的系统服务
```

#### 程序

```
# rpm -qa                # 查看所有安装的软件包
```


### 查看linux磁盘内存，以M为单位

```bash
[root@izbp1d9e2n6iw6mvtx07jmz imp-server]# df -m
Filesystem     1M-blocks  Used Available Use% Mounted on
/dev/vda1         100664 57533     37996  61% /
devtmpfs            3903     0      3903   0% /dev
tmpfs               3912     0      3912   0% /dev/shm
tmpfs               3912     1      3911   1% /run
tmpfs               3912     0      3912   0% /sys/fs/cgroup
tmpfs                783     0       783   0% /run/user/0
tmpfs                783     0       783   0% /run/user/997
```

```bash
PID：进程的ID
USER：进程所有者
PR：进程的优先级别，越小越优先被执行
NInice：值
VIRT：进程占用的虚拟内存
RES：进程占用的物理内存
SHR：进程使用的共享内存
S：进程的状态。S表示休眠，R表示正在运行，Z表示僵死状态，N表示该进程优先值为负数
%CPU：进程占用CPU的使用率
%MEM：进程使用的物理内存和总内存的百分比
TIME+：该进程启动后占用的总的CPU时间，即占用CPU使用时间的累加值。
COMMAND：进程启动命令名称
RSS: 表示相应进程在RAM中占用了多少内存
```

### 查看RAM运行内存

```bash
[root@izbp1d9e2n6iw6mvtx07jmz imp-server]# cat /proc/meminfo
MemTotal:        8010460 kB
MemFree:         1517064 kB
MemAvailable:    1441228 kB
Buffers:            2612 kB
Cached:           102868 kB
```

### 查询进程占用RAM情况

```bash
[root@izbp1d9e2n6iw6mvtx07jmz imp-server]# ps aux --sort=-rss
```

### centos7查看防火墙状态

```sh
[root@localhost volc]# systemctl status firewalld.service
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2019-10-05 09:50:55 CST; 1s ago
     Docs: man:firewalld(1)
Hint: Some lines were ellipsized, use -l to show in full.
[root@localhost volc]# systemctl status firewalld.service
```

### centos7开启防火墙

```bash
[root@localhost ~]# systemctl start firewalld.service
```

### 查看端口号开放情况

```bash
[root@localhost ~]# firewall-cmd --list-ports  
```

```bash
[root@localhost ~]# firewall-cmd --zone=public --add-port=80/tcp --permanent  
```

命令含义：
–zone #作用域
–add-port=80/tcp #添加端口，格式为：端口/通讯协议
–permanent #永久生效，没有此参数重启后失效

### 重启防火墙

#重启firewall  
firewall-cmd --reload  
#停止firewall  
systemctl stop firewalld.service  
#禁止firewall开机启动  
systemctl disable firewalld.service  

### 检查启动端口

```sh
[root@localhost bin]# netstat -an|grep 2181
tcp6       0      0 :::2181                 :::*                    LISTEN 
```

### `chkconfig`管理开机启动sh脚本配置

以zkServer.sh为例

#### 在`/etc/init.d/`目录下创建`autoStarZookeeper`文件
内容如下

```sh
#!/bin/bash
#chkconfig: 3 88 88
#description:zookepper开机自动启动
cd /usr/local/zookeeper-3.4.14/bin
./zkServer.sh start
```

#### 添加到chkconfig，开机自启动

```sh
[root@c69-01 ~]# chkconfig --add autoStarZookeeper
[root@c69-01 ~]# chkconfig --list autoStarZookeeper
autoStarZookeeper           	0:off	1:off	2:off	3:on	4:off	5:off	6:off
```

重新启动linux系统

#### 通过查看端口号是否占用验证`zookepper`开机启动配置是否生效

zookeeper服务设置的端口号是2181

```sh
alhost ~]$ netstat -an|grep 2181
tcp6       0      0 :::2181                 :::*                    LISTEN   
```

2181端口号已经被占用说明配置已经生效了

#### 从`chkconfig`管理中删除`autoStarZookeeper`

```sh
[root@c69-01 ~]# chkconfig --del autoStarZookeeper
```

### 查询端口号被哪个服务占用

```sh
# 查询2181端口号被哪个进程占用
[root@localhost volc]# lsof -i:2181
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
java    7165 root   24u  IPv6  47464      0t0  TCP *:eforward (LISTEN)
# 查询7165进程号信息
[root@localhost volc]# ps -aux | grep 7165
root       7165  0.1  1.1 2259728 11640 ?       Sl   23:11   0:01 java -Dzookeeper.log.dir=. -Dzookeeper.root.logger=INFO,CONSOLE -cp /usr/local/zookeeper-3.4.14/bin/../zookeeper-server/target/classes:/usr/local/zookeeper-3.4.14/bin/../build/classes:/usr/local/zookeeper-3.4.14/bin/../zookeeper-server/target/lib/*.jar:/usr/local/zookeeper-3.4.14/bin/../build/lib/*.jar:/usr/local/zookeeper-3.4.14/bin/../lib/slf4j-log4j12-1.7.25.jar:/usr/local/zookeeper-3.4.14/bin/../lib/slf4j-api-1.7.25.jar:/usr/local/zookeeper-3.4.14/bin/../lib/netty-3.10.6.Final.jar:/usr/local/zookeeper-3.4.14/bin/../lib/log4j-1.2.17.jar:/usr/local/zookeeper-3.4.14/bin/../lib/jline-0.9.94.jar:/usr/local/zookeeper-3.4.14/bin/../lib/audience-annotations-0.5.0.jar:/usr/local/zookeeper-3.4.14/bin/../zookeeper-3.4.14.jar:/usr/local/zookeeper-3.4.14/bin/../zookeeper-server/src/main/resources/lib/*.jar:/usr/local/zookeeper-3.4.14/bin/../conf: -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.local.only=false org.apache.zookeeper.server.quorum.QuorumPeerMain /usr/local/zookeeper-3.4.14/bin/../conf/zoo.cfg
root       9471  0.0  0.0 112708   976 pts/0    R+   23:21   0:00 grep --color=auto 7165
```
### 定位linux服务器问题命令

![linux-a](https://volc1612.gitee.io/blog/images/linux/linux-a.png)

### 查看定位指定字符串nohup.out文件

```sh
root[ffds]# grep enter 我是谁 nohup.out
```

### 本地使用scp命令上传和下载文件

上传

```sh
C:\Users\Administrator>scp F:\wk\learn\xdclass-mobile-redis\target\xdclass-mobile-redis-0.0.1-SNAPSHOT.jar root@192.168.25.11:/usr/local/src/project
root@192.168.25.11's password:
xdclass-mobile-redis-0.0.1-SNAPSHOT.jar                                               100%   30MB 135.8MB/s   00:00
C:\Users\Administrator>
```

下载

```sh
F:\wk>scp root@192.168.25.11:/usr/local/src/project/xdclass-mobile-redis-0.0.1-SNAPSHOT.jar F:\wk
root@192.168.25.11's password:
xdclass-mobile-redis-0.0.1-SNAPSHOT.jar                                               100%   30MB 137.0MB/s   00:00

F:\wk>
```


