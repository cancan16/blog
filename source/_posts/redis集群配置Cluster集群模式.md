---
title: redis集群配置Cluster集群模式
date: 2019-12-23 20:16:46
update: 2019-12-23 20:16:46
categories: Redis
tags: [redis]
---


<div style="text-align: center"><iframe height="60" width="260" src="https://www.ximalaya.com/thirdparty/player/sound/player.html?id=238394676&type=red" frameborder=0 allowfullscreen></iframe></div>

<img style="width: 80%;height:80%" src="https://volc1612.gitee.io/blog/images/redis集群配置Cluster集群模式/04212011.jpg"/>

<!-- more -->


### 下载编译安装redis

新建redis安装目录

/usr/local/share/redis
```bash
[root@ip-172-31-17-64 share]# mkdir redis
# 下载redis安装包
[root@ip-172-31-17-64 share]# wget http://download.redis.io/releases/redis-3.2.4.tar.gz
[root@ip-172-31-17-64 share]# tar -zxvf redis-3.2.4.tar.gz
[root@ip-172-31-17-64 share]# cd redis-3.2.4/
[root@ip-172-31-17-64 share]# make && make install
```

### 节点配置

```bash
# 创建redis_cluster目录
[root@ip-172-31-17-64 redis]# mkdir redis_cluster
[root@ip-172-31-17-64 redis]# cd redis_cluster
[root@ip-172-31-17-64 redis_cluster]# mkdir 7000 7001  7002 7003 7004 7005
# 进入redis-3.2.4目录,复制redis.conf到7000 7001 7002 7003 7004 7005中
[root@ip-172-31-17-64 redis-3.2.4]# cp redis.conf /usr/local/share/redis/redis_cluster/7000
[root@ip-172-31-17-64 redis-3.2.4]# cp redis.conf /usr/local/share/redis/redis_cluster/7001
[root@ip-172-31-17-64 redis-3.2.4]# cp redis.conf /usr/local/share/redis/redis_cluster/7002
[root@ip-172-31-17-64 redis-3.2.4]# cp redis.conf /usr/local/share/redis/redis_cluster/7003
[root@ip-172-31-17-64 redis-3.2.4]# cp redis.conf /usr/local/share/redis/redis_cluster/7004
[root@ip-172-31-17-64 redis-3.2.4]# cp redis.conf /usr/local/share/redis/redis_cluster/7005
```

#### 修改配置文件


分别进入集群目录修改redis.conf配置

修改以下配置项

```
# 修改成对应调的端口号
port 7000
# 注释掉 bind 配置
# bind 127.0.0.1
daemonize yes
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
appendonly yes
protected-mode no
```

### 启动redis

#### 把redis-server拷贝到redis_cluster目录下

```sh
[root@localhost redis-4.0.6]# cp src/redis-server redis_cluster/
```

分别进入集群目录启动

```bash
[root@ip-172-31-17-64 redis_cluster]# cd 7000
# 启动
[root@ip-172-31-17-64 7000]# ../redis-server redis.conf
[root@ip-172-31-17-64 7001]# ../redis-server redis.conf
```

### 客户端连接

![redis_a](https://volc1612.gitee.io/blog/images/redis集群配置Cluster集群模式/16a02274c25b8b47.png)

### 安装Ruby

```bash
[root@ip-172-31-17-64 7000]# yum install ruby
[root@ip-172-31-17-64 7000]# yum install rubygems
[root@ip-172-31-17-64 7000]# gem install redis -v 3.2.2
```



### 集群节点

```bash
# 进入redis根据目录的src目录，执行命令
# 52.77.243.30 为服务器外网ip
[root@ip-172-31-17-64 src]# ./redis-trib.rb create --replicas 1 52.77.243.30:7000 52.77.243.30:7001 52.77.243.30:7002 52.77.243.30:7003 52.77.243.30:7004 52.77.243.30:7005
>>> Creating cluster
>>> Performing hash slots allocation on 6 nodes...
Using 3 masters:
52.77.243.30:7000
52.77.243.30:7001
52.77.243.30:7002
Adding replica 52.77.243.30:7003 to 52.77.243.30:7000
Adding replica 52.77.243.30:7004 to 52.77.243.30:7001
Adding replica 52.77.243.30:7005 to 52.77.243.30:7002
M: 703b3f7c232f3bb99fc0004cef4ab0aac92f0b28 52.77.243.30:7000
   slots:0-5460 (5461 slots) master
M: 2464c0a5dcd7e0adf266951b5d1262871a96c819 52.77.243.30:7001
   slots:5461-10922 (5462 slots) master
M: 774b9a2d5bd214cfac0c6ded72ab5fa5bfcf2112 52.77.243.30:7002
   slots:10923-16383 (5461 slots) master
S: d000b91c9efffa7d2b55ca37cacd915698be22de 52.77.243.30:7003
   replicates 703b3f7c232f3bb99fc0004cef4ab0aac92f0b28
S: c1aae83e0324292254b6b81bfce53faf830ef9c1 52.77.243.30:7004
   replicates 2464c0a5dcd7e0adf266951b5d1262871a96c819
S: a2ecec5c754a73a33e4e14c16ea7cb6fe63b61dc 52.77.243.30:7005
   replicates 774b9a2d5bd214cfac0c6ded72ab5fa5bfcf2112
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join.......
>>> Performing Cluster Check (using node 52.77.243.30:7000)
M: 703b3f7c232f3bb99fc0004cef4ab0aac92f0b28 52.77.243.30:7000
   slots:0-5460 (5461 slots) master
   1 additional replica(s)
S: c1aae83e0324292254b6b81bfce53faf830ef9c1 52.77.243.30:7004
   slots: (0 slots) slave
   replicates 2464c0a5dcd7e0adf266951b5d1262871a96c819
S: a2ecec5c754a73a33e4e14c16ea7cb6fe63b61dc 52.77.243.30:7005
   slots: (0 slots) slave
   replicates 774b9a2d5bd214cfac0c6ded72ab5fa5bfcf2112
M: 2464c0a5dcd7e0adf266951b5d1262871a96c819 52.77.243.30:7001
   slots:5461-10922 (5462 slots) master
   1 additional replica(s)
S: d000b91c9efffa7d2b55ca37cacd915698be22de 52.77.243.30:7003
   slots: (0 slots) slave
   replicates 703b3f7c232f3bb99fc0004cef4ab0aac92f0b28
M: 774b9a2d5bd214cfac0c6ded72ab5fa5bfcf2112 52.77.243.30:7002
   slots:10923-16383 (5461 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
[root@ip-172-31-17-64 src]# redis-cli -c -p 7000
127.0.0.1:7000> quit
```

### 测试


```java
public void testRedisCluster() throws IOException{
		JedisPoolConfig pool = new JedisPoolConfig();
		// 设置最大的空闲数
		pool.setMaxIdle(2);
		// 设置最大连接数
		pool.setMaxTotal(30);
		Set<HostAndPort> nodes = new HashSet<>();
		nodes.add(new HostAndPort("52.77.243.30", 7001));
		nodes.add(new HostAndPort("52.77.243.30", 7002));
		nodes.add(new HostAndPort("52.77.243.30", 7003));
		nodes.add(new HostAndPort("52.77.243.30", 7004));
		nodes.add(new HostAndPort("52.77.243.30", 7005));
		nodes.add(new HostAndPort("52.77.243.30", 7000));
		JedisCluster jedis = new JedisCluster(nodes,pool );
		String name = jedis.set("name","lisi");
		System.out.println(name);
		jedis.close();
		pool.clone();
	}
```


![redis_b](https://volc1612.gitee.io/blog/images/redis集群配置Cluster集群模式/16a0228a74d43da2.png)

#### 效果

`7000`为主节点`7003`为其从节点，在`7000`节点上添加数据，就会复制到从节点`7003`

![redis集群测试.png](https://volc1612.gitee.io/blog/images/redis集群配置Cluster集群模式/redis集群测试.png)

### Cluster集群模式缺点

redis-cluster集群引入了主从模式，一个主节点对应一个或者多个从节点，当主节点宕机的时候，就会启用从节点。当其它主节点ping一个主节点A时，如果半数以上的主节点与A通信超时，那么认为主节点A宕机了。如果主节点A和它的从节点A1都宕机了，那么该集群的A和A1就无法再提供服务了，部分数据无法保存到redis了，集群不可用。

### redis集群讲解

![Redis集群特性讲解](https://volc1612.gitee.io/blog/images/redis集群配置Cluster集群模式/Redis集群特性讲解.jpg)

* 查看redis节点hash分布情况

```sh
[root@localhost src]# redis-cli -p 7000 cluster nodes | grep master
60414eda45a7cc4bd928413a8ff43cedd35120f3 192.168.25.11:7002@17002 master - 0 1578839152721 3 connected 10923-16383
436e691a2b8c4fcda2866700b94247ce9026fc7b 192.168.25.11:7001@17001 master - 0 1578839151000 2 connected 5461-10922
d288cf480c552f7aa93f446bb892e1f1fd5637e5 192.168.25.11:7000@17000 myself,master - 0 1578839152000 1 connected 0-5460
[root@localhost src]# 
```

#### 手把手测试故障转移

把`7000`主节点手动挂机，其从节点`7003`便会顶替作为主节点

```sh
[root@localhost src]# redis-cli -p 7000 debug segfault
Error: Server closed the connection
[root@localhost src]# redis-cli -p 7001 cluster nodes | grep master
436e691a2b8c4fcda2866700b94247ce9026fc7b 192.168.25.11:7001@17001 myself,master - 0 1578840065000 2 connected 5461-10922
60414eda45a7cc4bd928413a8ff43cedd35120f3 192.168.25.11:7002@17002 master - 0 1578840064000 3 connected 10923-16383
fc09960c6bde2b16777adf23409a6ae976cf17a8 192.168.25.11:7003@17003 master - 0 1578840066049 7 connected 0-5460
d288cf480c552f7aa93f446bb892e1f1fd5637e5 192.168.25.11:7000@17000 master,fail - 1578840048121 1578840046995 1 disconnected
[root@localhost src]# 
```


### Redis集群分片重hash

#### 手动处理solt节点槽重新分片

* 查看槽位分布

```sh
[root@localhost 7005]# redis-cli -p 7001 cluster nodes | grep master
fc09960c6bde2b16777adf23409a6ae976cf17a8 192.168.25.11:7003@17003 master - 0 1579219620602 7 connected 0-5460
bb0b020b3737751cc0daf3416d3cc6361c42bec3 192.168.25.11:7004@17004 master - 0 1579219619277 8 connected 5461-10922
a5ed549cb48e495977548e93746cbc66b22d3f99 192.168.25.11:7005@17005 master - 0 1579219619072 10 connected 10923-16383
```

* 移除7000节点100个槽位到7003节点中

```sh
[root@localhost src]# ./redis-trib.rb reshard 127.0.0.1:7000
>>> Performing Cluster Check (using node 127.0.0.1:7000)
S: d288cf480c552f7aa93f446bb892e1f1fd5637e5 127.0.0.1:7000
   slots: (0 slots) slave
   replicates fc09960c6bde2b16777adf23409a6ae976cf17a8
M: bb0b020b3737751cc0daf3416d3cc6361c42bec3 192.168.25.11:7004
   slots:5461-10922 (5462 slots) master
   1 additional replica(s)
S: 60414eda45a7cc4bd928413a8ff43cedd35120f3 192.168.25.11:7002
   slots: (0 slots) slave
   replicates a5ed549cb48e495977548e93746cbc66b22d3f99
S: 436e691a2b8c4fcda2866700b94247ce9026fc7b 192.168.25.11:7001
   slots: (0 slots) slave
   replicates bb0b020b3737751cc0daf3416d3cc6361c42bec3
M: a5ed549cb48e495977548e93746cbc66b22d3f99 192.168.25.11:7005
   slots:10923-16383 (5461 slots) master
   1 additional replica(s)
M: fc09960c6bde2b16777adf23409a6ae976cf17a8 192.168.25.11:7003
   slots:0-5460 (5461 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
How many slots do you want to move (from 1 to 16384)? 100
What is the receiving node ID? fc09960c6bde2b16777adf23409a6ae976cf17a8
Please enter all the source node IDs.
  Type 'all' to use all the nodes as source nodes for the hash slots.
  Type 'done' once you entered all the source nodes IDs.
Source node #1:all
...
Do you want to proceed with the proposed reshard plan (yes/no)? yes
```

* 查看槽位分布

```sh
[root@localhost src]# redis-cli -p 7001 cluster nodes | grep master
fc09960c6bde2b16777adf23409a6ae976cf17a8 192.168.25.11:7003@17003 master - 0 1579219943481 11 connected 0-5511 10923-10971
bb0b020b3737751cc0daf3416d3cc6361c42bec3 192.168.25.11:7004@17004 master - 0 1579219944000 8 connected 5512-10922
a5ed549cb48e495977548e93746cbc66b22d3f99 192.168.25.11:7005@17005 master - 0 1579219944490 10 connected 10972-16383
```

`7003`节点`0-5511`相比之前`0-5460`新增了`51`个槽位，`10923-10971`之间也新增了`49`个槽位。

#### 添加一个新的redis主节点

* 启动7006端口的redis主节点

```sh
[root@localhost 7006]# ../redis-server redis.conf
39674:C 07 Feb 20:08:09.943 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
39674:C 07 Feb 20:08:09.943 # Redis version=4.0.6, bits=64, commit=00000000, modified=0, pid=39674, just started
39674:C 07 Feb 20:08:09.943 # Configuration loaded
[root@localhost 7006]# ps -aux | grep redis
root      15661  0.1  0.3 151452 10004 ?        Ssl  05:09   1:13 ../redis-server *:7000 [cluster]
root      15822  0.1  0.4 151452 11952 ?        Ssl  05:09   1:13 ../redis-server *:7001 [cluster]
root      16219  0.1  0.1 151452  5172 ?        Ssl  05:09   1:13 ../redis-server *:7002 [cluster]
root      16377  0.1  0.1 151452  3236 ?        Ssl  05:09   1:13 ../redis-server *:7003 [cluster]
root      16494  0.1  0.2 151452  6316 ?        Ssl  05:09   1:12 ../redis-server *:7004 [cluster]
root      16596  0.1  0.2 151452  6352 ?        Ssl  05:09   1:12 ../redis-server *:7005 [cluster]
root      39675  0.0  0.0 147356  2756 ?        Ssl  20:08   0:00 ../redis-server *:7006 [cluster]
root      39810  0.0  0.0 112708   976 pts/1    R+   20:08   0:00 grep --color=auto redis
```

* 添加节点

默认是主节点

```sh
[root@localhost src]# ./redis-trib.rb add-node 127.0.0.1:7006 127.0.0.1:7000
>>> Adding node 127.0.0.1:7006 to cluster 127.0.0.1:7000
>>> Performing Cluster Check (using node 127.0.0.1:7000)
S: d288cf480c552f7aa93f446bb892e1f1fd5637e5 127.0.0.1:7000
   slots: (0 slots) slave
   replicates fc09960c6bde2b16777adf23409a6ae976cf17a8
M: bb0b020b3737751cc0daf3416d3cc6361c42bec3 192.168.25.11:7004
   slots:5512-10922 (5411 slots) master
   1 additional replica(s)
S: 60414eda45a7cc4bd928413a8ff43cedd35120f3 192.168.25.11:7002
   slots: (0 slots) slave
   replicates a5ed549cb48e495977548e93746cbc66b22d3f99
S: 436e691a2b8c4fcda2866700b94247ce9026fc7b 192.168.25.11:7001
   slots: (0 slots) slave
   replicates bb0b020b3737751cc0daf3416d3cc6361c42bec3
M: a5ed549cb48e495977548e93746cbc66b22d3f99 192.168.25.11:7005
   slots:10972-16383 (5412 slots) master
   1 additional replica(s)
M: fc09960c6bde2b16777adf23409a6ae976cf17a8 192.168.25.11:7003
   slots:0-5511,10923-10971 (5561 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
>>> Send CLUSTER MEET to node 127.0.0.1:7006 to make it join the cluster.
[OK] New node added correctly.
[root@localhost src]# redis-cli -p 7001 cluster nodes | grep master
75eb46fb6f45a0dbabf253a44737d2597022662f 192.168.25.11:7006@17006 master - 0 1581077553171 0 connected
fc09960c6bde2b16777adf23409a6ae976cf17a8 192.168.25.11:7003@17003 master - 0 1581077552557 11 connected 0-5511 10923-10971
bb0b020b3737751cc0daf3416d3cc6361c42bec3 192.168.25.11:7004@17004 master - 0 1581077552000 8 connected 5512-10922
a5ed549cb48e495977548e93746cbc66b22d3f99 192.168.25.11:7005@17005 master - 0 1581077552155 10 connected 10972-16383
```

* 为新的主节点分配槽位

将7000节点1000个槽位分配给7006

```sh
[root@localhost src]# ./redis-trib.rb reshard 127.0.0.1:7000
>>> Performing Cluster Check (using node 127.0.0.1:7000)
S: d288cf480c552f7aa93f446bb892e1f1fd5637e5 127.0.0.1:7000
   slots: (0 slots) slave
   replicates fc09960c6bde2b16777adf23409a6ae976cf17a8
M: bb0b020b3737751cc0daf3416d3cc6361c42bec3 192.168.25.11:7004
   slots:5461-10922 (5462 slots) master
   1 additional replica(s)
S: 60414eda45a7cc4bd928413a8ff43cedd35120f3 192.168.25.11:7002
   slots: (0 slots) slave
   replicates a5ed549cb48e495977548e93746cbc66b22d3f99
S: 436e691a2b8c4fcda2866700b94247ce9026fc7b 192.168.25.11:7001
   slots: (0 slots) slave
   replicates bb0b020b3737751cc0daf3416d3cc6361c42bec3
M: a5ed549cb48e495977548e93746cbc66b22d3f99 192.168.25.11:7005
   slots:10923-16383 (5461 slots) master
   1 additional replica(s)
M: fc09960c6bde2b16777adf23409a6ae976cf17a8 192.168.25.11:7003
   slots:0-5460 (5461 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
How many slots do you want to move (from 1 to 16384)? 1000
What is the receiving node ID? fc09960c6bde2b16777adf23409a6ae976cf17a8
Please enter all the source node IDs.
  Type 'all' to use all the nodes as source nodes for the hash slots.
  Type 'done' once you entered all the source nodes IDs.
Source node #1:all
...
Do you want to proceed with the proposed reshard plan (yes/no)? yes
```

* 再次查看新增节点7006槽位分配

```sh
[root@localhost src]# redis-cli -p 7001 cluster nodes | grep master
75eb46fb6f45a0dbabf253a44737d2597022662f 192.168.25.11:7006@17006 master - 0 1581077908000 12 connected 0-339 5512-5841 10972-11301
fc09960c6bde2b16777adf23409a6ae976cf17a8 192.168.25.11:7003@17003 master - 0 1581077909000 11 connected 340-5511 10923-10971
bb0b020b3737751cc0daf3416d3cc6361c42bec3 192.168.25.11:7004@17004 master - 0 1581077908000 8 connected 5842-10922
a5ed549cb48e495977548e93746cbc66b22d3f99 192.168.25.11:7005@17005 master - 0 1581077908584 10 connected 11302-16383
```

#### 新增从节点7007

* 启动节点7007

* 添加7007作为从节点到集群

```sh
[root@localhost src]# ./redis-trib.rb add-node --slave 127.0.0.1:7007 127.0.0.1:7001
>>> Adding node 127.0.0.1:7007 to cluster 127.0.0.1:7001
>>> Performing Cluster Check (using node 127.0.0.1:7001)
S: 436e691a2b8c4fcda2866700b94247ce9026fc7b 127.0.0.1:7001
   slots: (0 slots) slave
   replicates bb0b020b3737751cc0daf3416d3cc6361c42bec3
S: d288cf480c552f7aa93f446bb892e1f1fd5637e5 192.168.25.11:7000
   slots: (0 slots) slave
   replicates fc09960c6bde2b16777adf23409a6ae976cf17a8
M: 75eb46fb6f45a0dbabf253a44737d2597022662f 192.168.25.11:7006
   slots:0-339,5512-5841,10972-11301 (1000 slots) master
   0 additional replica(s)
M: fc09960c6bde2b16777adf23409a6ae976cf17a8 192.168.25.11:7003
   slots:340-5511,10923-10971 (5221 slots) master
   1 additional replica(s)
M: bb0b020b3737751cc0daf3416d3cc6361c42bec3 192.168.25.11:7004
   slots:5842-10922 (5081 slots) master
   1 additional replica(s)
M: a5ed549cb48e495977548e93746cbc66b22d3f99 192.168.25.11:7005
   slots:11302-16383 (5082 slots) master
   1 additional replica(s)
S: 60414eda45a7cc4bd928413a8ff43cedd35120f3 192.168.25.11:7002
   slots: (0 slots) slave
   replicates a5ed549cb48e495977548e93746cbc66b22d3f99
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
Automatically selected master 192.168.25.11:7006
>>> Send CLUSTER MEET to node 127.0.0.1:7007 to make it join the cluster.
Waiting for the cluster to join.
>>> Configure node as replica of 192.168.25.11:7006.
[OK] New node added correctly.
[root@localhost src]# redis-cli -p 7001 cluster nodes
d288cf480c552f7aa93f446bb892e1f1fd5637e5 192.168.25.11:7000@17000 slave fc09960c6bde2b16777adf23409a6ae976cf17a8 0 1581078601500 11 connected
75eb46fb6f45a0dbabf253a44737d2597022662f 192.168.25.11:7006@17006 master - 0 1581078600485 12 connected 0-339 5512-5841 10972-11301
fc09960c6bde2b16777adf23409a6ae976cf17a8 192.168.25.11:7003@17003 master - 0 1581078601000 11 connected 340-5511 10923-10971
436e691a2b8c4fcda2866700b94247ce9026fc7b 127.0.0.1:7001@17001 myself,slave bb0b020b3737751cc0daf3416d3cc6361c42bec3 0 1581078601000 2 connected
bb0b020b3737751cc0daf3416d3cc6361c42bec3 192.168.25.11:7004@17004 master - 0 1581078601000 8 connected 5842-10922
0b884df3bf9db628eed12564af571f509fbc2628 127.0.0.1:7007@17007 slave 75eb46fb6f45a0dbabf253a44737d2597022662f 0 1581078602519 12 connected
a5ed549cb48e495977548e93746cbc66b22d3f99 192.168.25.11:7005@17005 master - 0 1581078602013 10 connected 11302-16383
60414eda45a7cc4bd928413a8ff43cedd35120f3 192.168.25.11:7002@17002 slave a5ed549cb48e495977548e93746cbc66b22d3f99 0 1581078601000 10 connected
```

`127.0.0.1:7007@17007 slave 75eb46fb6f45a0dbabf253a44737d2597022662f`: 7007节点作为从节点，它的主节点ID是`75eb46fb6f45a0dbabf253a44737d2597022662f`，也就是默认把7006节点作为它的主节点。

#### 移除一个节点

移除7007节点

```sh
[root@localhost src]# ./redis-trib.rb del-node 127.0.0.1:7000 0b884df3bf9db628eed12564af571f509fbc2628
>>> Removing node 0b884df3bf9db628eed12564af571f509fbc2628 from cluster 127.0.0.1:7000
>>> Sending CLUSTER FORGET messages to the cluster...
>>> SHUTDOWN the node.
[root@localhost src]# redis-cli -p 7001 cluster nodes
d288cf480c552f7aa93f446bb892e1f1fd5637e5 192.168.25.11:7000@17000 slave fc09960c6bde2b16777adf23409a6ae976cf17a8 0 1581079377518 11 connected
75eb46fb6f45a0dbabf253a44737d2597022662f 192.168.25.11:7006@17006 master - 0 1581079377929 12 connected 0-339 5512-5841 10972-11301
fc09960c6bde2b16777adf23409a6ae976cf17a8 192.168.25.11:7003@17003 master - 0 1581079377518 11 connected 340-5511 10923-10971
436e691a2b8c4fcda2866700b94247ce9026fc7b 127.0.0.1:7001@17001 myself,slave bb0b020b3737751cc0daf3416d3cc6361c42bec3 0 1581079376000 2 connected
bb0b020b3737751cc0daf3416d3cc6361c42bec3 192.168.25.11:7004@17004 master - 0 1581079376594 8 connected 5842-10922
a5ed549cb48e495977548e93746cbc66b22d3f99 192.168.25.11:7005@17005 master - 0 1581079376000 10 connected 11302-16383
60414eda45a7cc4bd928413a8ff43cedd35120f3 192.168.25.11:7002@17002 slave a5ed549cb48e495977548e93746cbc66b22d3f99 0 1581079377000 10 connected
[root@localhost src]# 
```

* 移除主节点【先确保节点里面没有slot】
  * 使用同样的方法移除主节点,不过在移除主节点前，需要确保这个主节点是空的. 如果不是空的,需要将这个节点的数据重新分片到其他主节点上.
  * 替代移除主节点的方法是手动执行故障恢复，被移除的主节点会作为一个从节点存在，不过这种情况下不会减少集群节点的数量，也需要重新分片数据.
* 移除从节点-直接移除成功

