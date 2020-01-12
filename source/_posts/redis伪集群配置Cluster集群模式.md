---
title: redis伪集群配置Cluster集群模式
date: 2019-12-23 20:16:46
update: 2019-12-23 20:16:46
categories: Redis
tags: [redis]
---


<div style="text-align: center"><iframe height="60" width="260" src="https://www.ximalaya.com/thirdparty/player/sound/player.html?id=238394676&type=red" frameborder=0 allowfullscreen></iframe></div>

<img style="width: 80%;height:80%" src="https://volc1612.gitee.io/blog/images/redis伪集群配置Cluster集群模式/04212011.jpg"/>

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

![redis_a](https://volc1612.gitee.io/blog/images/redis伪集群配置Cluster集群模式/16a02274c25b8b47.png)

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


![redis_b](https://volc1612.gitee.io/blog/images/redis伪集群配置Cluster集群模式/16a0228a74d43da2.png)

### Cluster集群模式缺点

redis-cluster集群引入了主从模式，一个主节点对应一个或者多个从节点，当主节点宕机的时候，就会启用从节点。当其它主节点ping一个主节点A时，如果半数以上的主节点与A通信超时，那么认为主节点A宕机了。如果主节点A和它的从节点A1都宕机了，那么该集群的A和A1就无法再提供服务了，部分数据无法保存到redis了，集群不可用。

### redis集群讲解

![Redis集群特性讲解](https://volc1612.gitee.io/blog/images/redis伪集群配置Cluster集群模式/Redis集群特性讲解.jpg)
