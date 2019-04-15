---
title: Dubbo+zookeeper
date: 2016-12-12 11:19:29
update: 2016-12-12 11:19:29
categories: DUBBO
tags: [dubbo, zookeeper, linux, tomcat]
---
 
# 安装zookeeper

1.下载安装包  
下载地址：http://mirrors.hust.edu.cn/apache/zookeeper/zookeeper-3.4.6/
<!-- more -->
在/webapp下解压安装文件
```apache
tar -xzvf zookeeper-3.4.5.tar.gz
```
/webapp/zookeeper-3.4.5下创建logs文件夹
2.zookeeper的环境变量的配置  

在/etc/profile文件中加入如下的内容：  
	
```apache
	#set zookeeper environment
	export ZOOKEEPER_HOME=/home/haduser/zookeeper/zookeeper-3.4.5
	export PATH=$PATH:$ZOOKEEPER_HOME/bin:$ZOOKEEPER_HOME/conf
```
3.集群部署  
	在Zookeeper集群环境下只要一半以上的机器正常启动了，那么Zookeeper服务将是可用的。因此，集群上部署Zookeeper最好使用奇数台机器，这样如果有5台机器，只要3台正常工作则服务将正常使用。
下面我们将对Zookeeper的配置文件的参数进行设置：

进入zookeeper-3.4.5/conf
	
```apache
	$:cp zoo_sample.cfg zoo.cfg
	$:vim zoo.cfg	
```
	
	
![1](https://volc1612.gitee.io/blog/images/DUBBO+ZOOKEEPER/1.png)   

注意上图的配置中master，slave1分别为主机名，具体的对应的主机可参见之前的Hadoop的安装与配置的博文  
在上面的配置文件中"server.id=host:port:port"中的第一个port是从机器（follower）连接到主机器（leader）的端口号，第二个port是进行leadership选举的端口号。  

接下来在dataDir所指定的目录下创建一个文件名为myid的文件，文件中的内容只有一行，为本主机对应的id值，也就是上图中server.id中的id。例如：在服务器1中的myid的内容应该写入1。

4.远程复制分发安装文件  

接下来将上面的安装文件拷贝到集群中的其他机器上对应的目录下：  
```apache
	haduser@master:~/zookeeper$ scp -r zookeeper-3.4.5/ slave1:/home/haduser/zookeeper/zookeeper-3.4.5  
	haduser@master:~/zookeeper$ scp -r zookeeper-3.4.5/ slave2:/home/haduser/zookeeper/zookeeper-3.4.5  
```
拷贝完成后修改对应的机器上的myid。例如修改slave1中的myid如下：  
```apache
	haduser@slave1:~/zookeeper/zookeeper-3.4.5$ echo "2" > data/myid  
	haduser@slave1:~/zookeeper/zookeeper-3.4.5$ cat data/myid  
```
内容为：2    

	
	
5.启动ZooKeeper集群  
	在ZooKeeper集群的每个结点上，执行启动ZooKeeper服务的脚本，如下所示： 
```apache	
	  haduser@master:~/zookeeper/zookeeper-3.4.5$ bin/zkServer.sh start
	  haduser@slave1:~/zookeeper/zookeeper-3.4.5$ bin/zkServer.sh start
	  haduser@slave2:~/zookeeper/zookeeper-3.4.5$ bin/zkServer.sh start
```
![2](https://volc1612.gitee.io/blog/images/DUBBO+ZOOKEEPER/2.png)  
其中，QuorumPeerMain是zookeeper进程，启动正常。  
如上依次启动了所有机器上的Zookeeper之后可以通过ZooKeeper的脚本来查看启动状态，包括集群中各个结点的角色（或是Leader，或是Follower），如下所示，是在ZooKeeper集群中的每个结点上查询的结果：
	
![3](https://volc1612.gitee.io/blog/images/DUBBO+ZOOKEEPER/3.png)  
通过上面状态查询结果可见，slave1是集群的Leader，其余的两个结点是Follower。  

另外，可以通过客户端脚本，连接到ZooKeeper集群上。对于客户端来说，ZooKeeper是一个整体（ensemble），连接到ZooKeeper集群实际上感觉在独享整个集群的服务，所以，你可以在任何一个结点上建立到服务集群的连接，例如：
查看是否安装启动成功  
```apache
	bin/zkCli.sh -server 116.62.2.289:2181
```
![4](https://volc1612.gitee.io/blog/images/DUBBO+ZOOKEEPER/4.png) 

脚本启动
脚本内容：
```apache
aaa=` ps aux| grep "/webapp/zookeeper" | grep -v grep|awk '{print $2}'`

if [ "$aaa" = "" ];
then
  (/webapp/zookeeper-3.4.5/bin/zkServer.sh start) && (echo "Start zookeeper  Service  Successful!") || (echo "Start zookeeper Service Fault")
else
   echo ""
   echo "zookeeper is runging"
   echo ""

fi

```
启动脚本
```apache
sh zkServer.sh
```

启动结果：
```apache
	[root@iZbp199af3f3y8j7fr8kolZ bin]# sh stop_zookeeper.sh 
	 zookeeper is not runging 
	[root@iZbp199af3f3y8j7fr8kolZ bin]# sh start_zookeeper.sh 
	JMX enabled by default
	Using config: /webapp/zookeeper-3.4.5/bin/../conf/zoo.cfg
	Starting zookeeper ... STARTED
	Start zookeeper  Service  Successful!
```

配置参考：http://www.cnblogs.com/yuyijq/p/3438829.html  
配置参考：http://343614597.blog.51cto.com/7056394/1874745 
启动zookeeper  
	在zookper-3.3.6/bin目录下执行  
```apache
	./zkServer.sh start 启动zookeeper  
```
验证zookeeper是否启动  
```apache
netstat -lpn | grep 2181  
```
# 安装DUBBO  

1.下载dubbo安装包  

2.在根目录创建相应文件夹webapp/deploy/  
并解压安装文件到deploy文件夹中  
	
3.配置  
	/webapp/deploy/dubbo-admin/WEB-INF  
	中的dubbo.properties文件  
	内容如下：  
```apache
	dubbo.registry.address=zookeeper://127.0.0.1:2181  
	dubbo.admin.root.password=root  
	dubbo.admin.guest.password=guest  
```
	
4.然后启动tomcat服务，显示登陆页面，说明dubbo-admin部署成功  
	访问路径：http://服务器IP:8080/  
	账号：root  
	密码：root  
	
