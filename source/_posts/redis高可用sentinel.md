---
title: redis高可用sentinel
date: 2019-12-17 21:26:47
update: 2019-12-17 21:26:47
categories: Redis
tags: [redis,sentinel]
---


<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=280 height=86 src="//music.163.com/outchain/player?type=2&id=1409296047&auto=0&height=66"></iframe>


<img style="width: 80%;height:80%" src="https://volc1612.gitee.io/blog/images/redis高可用sentinel/cat.jpg" />

<!-- more -->

### redis高可用

* 高可用的概念

高可用HA（High Availability）是分布式系统架构设计中必须考虑的因素之一，它通常是指，通过设计减少系统不能提供服务的时间。

- 通过三大要点解释高可用:

  ****************

  - 单点是系统高可用的大敌，应该尽量在系统设计的过程中避免单点

  - 保证系统高可用，架构设计的核心准则是：冗余。

  - 每次出现故障需要人工介入恢复势必会增加系统的不可服务实践,实现自动故障转移

  - 重启服务，看服务是否每次都获取锁失败   
------

- 分布式高可用经典架构环节分析

  - 【客户端层】到【反向代理层】的高可用，是通过反向代理层的冗余来实现的。以nginx为例：有两台nginx，一台对线上提供服务，另一台冗余以保证高可用， 常见的实践是keepalived存活探测
  - 【反向代理层】到【web应用】的高可用，是通过站点层的冗余来实现的。假设反向代理层是nginx，nginx.conf里能够配置多个web后端，并且nginx能够探测到多个后端的存活性。
    * 自动故障转移：当web-server挂了的时候，nginx能够探测到，会自动的进行故障转移，将流量自动迁移到其他的web-server，整个过程由nginx自动完成，对调用方是透明的。

  * 【服务层】到【缓存层】的高可用，是通过缓存数据的冗余来实现的。
              redis天然支持主从同步，redis官方也有sentinel哨兵机制，来做redis的存活性检测。
  * 【服务层】到【数据库层】的高可用，数据库层用“主从同步，读写分离”架构，所以数据库层的高可用，又分为“读库高可用”与“写库高可用”两类。
    * 读库采用冗余的方式实现高可用，写库采用keepalived存活探测   binlog进行同步

[](#jump)
![redis高可用之哨兵模式](https://volc1612.gitee.io/blog/images/redis高并发高可用/redis高可用之哨兵模式.png)

### Redis高可用架构Sentinel

#### 灾备切换Sentinel的使用

##### 手动模拟Sentinel功能

有三台启动的redis接口

```sh
[root@localhost redis-4.0.6]# ps -aux | grep redis
root     101389  0.0  0.0 142928  2248 ?        Ssl  19:04   0:03 ./src/redis-server *:6379
root     101400  0.0  0.0 142944  2232 ?        Ssl  19:04   0:03 ./src/redis-server *:6380
root     101446  0.0  0.0 142960  2232 ?        Ssl  19:06   0:03 ./src/redis-server *:6381
root     102804  0.0  0.0 112708   976 pts/1    R+   20:20   0:00 grep --color=auto redis
[root@localhost redis-4.0.6]# 
```

查看三个节点信息

```sh
[root@localhost redis-4.0.6]# ./src/redis-cli -p 6379
127.0.0.1:6379> info replication
# Replication
role:master
connected_slaves:2
slave0:ip=127.0.0.1,port=6380,state=online,offset=6560,lag=0
slave1:ip=127.0.0.1,port=6381,state=online,offset=6546,lag=1
master_replid:a65d77d76690218bfc290701e6d2a6da0e35997e
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:6560
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:6560
127.0.0.1:6379> 
```

6379作为主节点，6380和6381作为从节点。

* 把6379主节点停止(服务宕机)

```sh
127.0.0.1:6379> shutdown save
not connected> 
```
* 把6381设置为主节点

```sh
127.0.0.1:6380> slaveof no one
OK
127.0.0.1:6380> info replication
# Replication
role:master
connected_slaves:0
master_replid:686c89241d8161d8d78c1ab6e8ba471bc5ba3d6a
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:6700
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:6700
127.0.0.1:6380> 
```

* 把6380变成6381的主节点

```sh
127.0.0.1:6380> exit
[root@localhost redis-4.0.6]# ./src/redis-cli -p 6381
127.0.0.1:6381> slaveof 127.0.0.1 6380
OK
127.0.0.1:6381> info replication
# Replication
role:slave
master_host:127.0.0.1
master_port:6380
master_link_status:up
master_last_io_seconds_ago:7
master_sync_in_progress:0
slave_repl_offset:6700
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:e71bd6fc66c8ed23a4b359d7365d5c1d654bf4a0
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:6700
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:6701
repl_backlog_histlen:0
127.0.0.1:6381> 
```

#### 使用sentinel

* 复制`sentinel.conf`到`redis-cluster`

```sh
[root@localhost redis-4.0.6]# cp sentinel.conf redis-cluster/
[root@localhost redis-4.0.6]# cd redis-cluster/
[root@localhost redis-cluster]# ll
total 20
drwxr-xr-x. 2 root root 4096 Dec  8 22:29 6379
drwxr-xr-x. 2 root root 4096 Dec 11 21:28 6380
drwxr-xr-x. 2 root root 4096 Dec 12 20:52 6381
-rw-r--r--. 1 root root 7606 Dec 12 21:10 sentinel.conf
[root@localhost redis-cluster]# 
```

* 开启配置`sentinel.conf`

```
sentinel monitor mymaster 127.0.0.1 6379 1   
sentinel down-after-milliseconds mymaster 10000
sentinel failover-timeout mymaster 60000
sentinel parallel-syncs mymaster 1
```

* 启动sentinel

```sh
[root@localhost redis-4.0.6]# ./src/redis-sentinel redis-cluster/sentinel.conf 
103750:X 12 Dec 21:24:09.277 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
103750:X 12 Dec 21:24:09.277 # Redis version=4.0.6, bits=64, commit=00000000, modified=0, pid=103750, just started
103750:X 12 Dec 21:24:09.277 # Configuration loaded
103750:X 12 Dec 21:24:09.277 * Increased maximum number of open files to 10032 (it was originally set to 1024).
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 4.0.6 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in sentinel mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 26379
 |    `-._   `._    /     _.-'    |     PID: 103750
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

103750:X 12 Dec 21:24:09.278 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
103750:X 12 Dec 21:24:09.278 # Sentinel ID is f7547863b810f8e3b0a014fb793e14f503f9fdad
103750:X 12 Dec 21:24:09.278 # +monitor master mymaster 127.0.0.1 6379 quorum 1
103750:X 12 Dec 21:24:09.280 * +slave slave 127.0.0.1:6380 127.0.0.1 6380 @ mymaster 127.0.0.1 6379
103750:X 12 Dec 21:24:09.281 * +slave slave 127.0.0.1:6381 127.0.0.1 6381 @ mymaster 127.0.0.1 6379
```
从启动日志中可以明确看出6379为主节点，6380和6381为从节点

* 关闭7379主节点,查看sentinel日志

```sh
[root@localhost redis-4.0.6]# ./src/redis-cli -p 6379 shutdown
[root@localhost redis-4.0.6]# 
```

sentinel监控日志

```
103750:X 12 Dec 21:27:34.231 # +sdown master mymaster 127.0.0.1 6379
103750:X 12 Dec 21:27:34.231 # +odown master mymaster 127.0.0.1 6379 #quorum 1/1
103750:X 12 Dec 21:27:34.231 # +new-epoch 2
103750:X 12 Dec 21:27:34.231 # +try-failover master mymaster 127.0.0.1 6379
103750:X 12 Dec 21:27:34.232 # +vote-for-leader f7547863b810f8e3b0a014fb793e14f503f9fdad 2
103750:X 12 Dec 21:27:34.232 # +elected-leader master mymaster 127.0.0.1 6379
103750:X 12 Dec 21:27:34.232 # +failover-state-select-slave master mymaster 127.0.0.1 6379
103750:X 12 Dec 21:27:34.303 # +selected-slave slave 127.0.0.1:6381 127.0.0.1 6381 @ mymaster 127.0.0.1 6379
103750:X 12 Dec 21:27:34.303 * +failover-state-send-slaveof-noone slave 127.0.0.1:6381 127.0.0.1 6381 @ mymaster 127.0.0.1 6379
103750:X 12 Dec 21:27:34.356 * +failover-state-wait-promotion slave 127.0.0.1:6381 127.0.0.1 6381 @ mymaster 127.0.0.1 6379
103750:X 12 Dec 21:27:35.267 # +promoted-slave slave 127.0.0.1:6381 127.0.0.1 6381 @ mymaster 127.0.0.1 6379
103750:X 12 Dec 21:27:35.267 # +failover-state-reconf-slaves master mymaster 127.0.0.1 6379
103750:X 12 Dec 21:27:35.357 * +slave-reconf-sent slave 127.0.0.1:6380 127.0.0.1 6380 @ mymaster 127.0.0.1 6379
103750:X 12 Dec 21:27:36.312 * +slave-reconf-inprog slave 127.0.0.1:6380 127.0.0.1 6380 @ mymaster 127.0.0.1 6379
103750:X 12 Dec 21:27:37.321 * +slave-reconf-done slave 127.0.0.1:6380 127.0.0.1 6380 @ mymaster 127.0.0.1 6379
103750:X 12 Dec 21:27:37.392 # +failover-end master mymaster 127.0.0.1 6379
103750:X 12 Dec 21:27:37.392 # +switch-master mymaster 127.0.0.1 6379 127.0.0.1 6381
103750:X 12 Dec 21:27:37.393 * +slave slave 127.0.0.1:6380 127.0.0.1 6380 @ mymaster 127.0.0.1 6381
103750:X 12 Dec 21:27:37.393 * +slave slave 127.0.0.1:6379 127.0.0.1 6379 @ mymaster 127.0.0.1 6381
103750:X 12 Dec 21:27:47.401 # +sdown slave 127.0.0.1:6379 127.0.0.1 6379 @ mymaster 127.0.0.1 6381
```

从日志中可以看出`+switch-master mymaster 127.0.0.1 6379 127.0.0.1 6381`6381替代了6380作为主节点，我们登陆6381进行验证

* 验证故障转移是否生效

```sh
[root@localhost redis-4.0.6]# ./src/redis-cli -p 6381
127.0.0.1:6381> info replication
# Replication
role:master
connected_slaves:1
slave0:ip=127.0.0.1,port=6380,state=online,offset=30776,lag=1
master_replid:5f978d7756e69e16ae5e28a8064e43e98574f811
master_replid2:10584b87ac8a21e262a9bf1173a5c2ea4dc677c6
master_repl_offset:30909
second_repl_offset:12820
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:30909
127.0.0.1:6381> 
```

节点信息显示`6381`已经作为了主节点，有一个活跃从节点为`6380`。

#### 命令解析

* sentinel monitor mymaster 127.0.0.1 6379 1

    mymaster的主节点名称，1表示将这个主服务器判断为失效至少需要1个`Sentinel`同意（只要同意 Sentinel 的数量不达标，自动故障迁移就不会执行），如果redis节点分布为3主6从，那么这个值可以为2，至少需要两个从点同意。

* down-after-milliseconds

    选项指定了`Sentinel`认为服务器已经断线所需的毫秒数

*  failover（故障切换）

    过期时间，当`failover`开始后，在此时间内仍然没有触发任何`failover`操作，当前`sentinel`将会认为此次`failoer`失败

* parallel-syncs

    选项指定了在执行故障转移时， 最多可以有多少个从服务器同时对新的主服务器进行同步， 这个数字越小， 完成故障转移所需的时间就越长。

* 如果从服务器被设置为允许使用过期数据集， 那么你可能不希望所有从服务器都在同一时间向新的主服务器发送同步请求， 因为尽管复制过程的绝大部分步骤都不会阻塞从服务器， 但从服务器在载入主服务器发来的 RDB 文件时， 仍然会造成从服务器在一段时间内不能处理命令请求： 如果全部从服务器一起对新的主服务器进行同步， 那么就可能会造成所有从服务器在短时间内全部不可用的情况出现。

#### Sentinel三大任务讲解

##### 冷备和热备概念

![高可用数据备份方案](https://volc1612.gitee.io/blog/images/redis高并发高可用/高可用数据备份方案.jpg)

* 冷备  

    *  概念：冷备份发生在数据库已经正常关闭的情况下，当正常关闭时会提供给我们一个完整的数据库
    * 优点：
      * 是非常快速的备份方法（只需拷文件）
      *  低度维护，高度安全
    * 缺点：
      - 单独使用时，只能提供到“某一时间点上”的恢复
      - 再实施备份的全过程中，数据库必须要作备份而不能作其他工作。也就是说，在冷备份过程中，数据库必须是关闭状态

* 热备

    *   概念：热备份是在数据库运行的情况下，采用archivelog mode方式备份数据库的方法      

    *   优点：
        * 备份的时间短
        * 备份时数据库仍可使用
        * 可达到秒级恢复
    *   缺点  
        * 若热备份不成功，所得结果不可用于时间点的恢复
        * 因难于维护，所以要非凡仔细小心

##### Sentinel三大任务讲解

- 监控（Monitoring）： Sentinel 会不断地检查你的主服务器和从服务器是否运作正常。
- 提醒（Notification）： 当被监控的某个 Redis 服务器出现问题时， Sentinel 可以通过 API 向管理员或者其他应用程序发送通知。      
- 自动故障迁移（Automatic failover）： 当一个主服务器不能正常工作时， Sentinel 会开始一次自动故障迁移操作， 它会将失效主服务器的其中一个从服务器升级为新的主服务器， 并让失效主服务器的其他从服务器改为复制新的主服务器； 当客户端试图连接失效的主服务器时， 集群也会向客户端返回新主服务器的地址， 使得集群可以使用新主服务器代替失效服务器。

#### Redis高可用Sentinel故障转移原理

##### Sentinel是如何工作的

* 主观下线概念：

  - 概念主观下线（Subjectively Down， 简称 SDOWN）指的是单个 Sentinel 实例对服务器做出的下线判断

  - 主管下线特点：

    * 如果一个服务器没有在 master-down-after-milliseconds 选项所指定的时间内， 对向它发送 PING 命令的 Sentinel 返回一个有效回复（valid reply）， 那么 Sentinel 就会将这个服务器标记为主观下线

    * 服务器对 PING 命令的有效回复可以是以下三种回复的其中一种：

        ```shell
        返回 +PONG 。
        返回 -LOADING 错误。
        返回 -MASTERDOWN 错误。
        ```
* 客观下线

  - 客观下线概念：

    * 指的是多个 Sentinel 实例在对同一个服务器做出 SDOWN 判断， 并且通过 SENTINEL is-master-down-by-addr 命令互相交流之后， 得出的服务器下线判断。 （一个 Sentinel 可以通过向另一个 Sentinel 发送 SENTINEL is-master-down-by-addr 命令来询问对方是否认为给定的服务器已下线。）

  - 客观下线特点：

    * 从主观下线状态切换到客观下线状态并没有使用严格的法定人数算法（strong quorum algorithm）， 而是使用了流言协议： 如果 Sentinel 在给定的时间范围内， 从其他 Sentinel 那里接收到了足够数量的主服务器下线报告， 那么 Sentinel 就会将主服务器的状态从主观下线改变为客观下线。 如果之后其他 Sentinel 不再报告主服务器已下线， 那么客观下线状态就会被移除。

  - 客观下线注意点：

    * 客观下线条件只适用于主服务器： 对于任何其他类型的 Redis 实例， Sentinel 在将它们判断为下线前不需要进行协商， 所以从服务器或者其他 Sentinel 永远不会达到客观下线条件。 只要一个 Sentinel 发现某个主服务器进入了客观下线状态， 这个 Sentinel 就可能会被其他 Sentinel 推选出， 并对失效的主服务器执行自动故障迁移操作。

### springboot整合sentinel

由<span id = "jump">sentinel哨兵模式</span>图解可知，`sentinel`类似`nginx`进行代理服务，所以项目中只需要连接`sentinel`即可，无需单独配置连接`redis`节点。

* 配置文件

```
redis:
    sentinel:
        master: mymaster  #mymaster
        nodes: 172.16.244.133:26379
```

  

* 引入依赖

```java
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

