---
title: redis缓存持久化策略
date: 2020-03-01 17:26:47
update: 2020-03-01 17:26:47
categories: Redis
tags: [redis]
---

<div style="text-align: center"><iframe height="60" width="260" src="https://www.ximalaya.com/thirdparty/player/sound/player.html?id=46530962&type=red" frameborder=0 allowfullscreen></iframe></div>

<img style="width: 80%;height:80%" src="https://volc1612.gitee.io/blog/images/redis缓存持久化策略/济州岛.jpg" />

<!-- more -->

### Redis RDB持久化原理

**简介：rdb持久化方案配置讲解，redis的开发者是怎么实现rdb的**

- rdb持久化配置

  ```shell
  # 时间策略，表示900s内如果有1条是写入命令，就触发产生一次快照，可以理解为就进行一次备份
  save 900 1
  save 300 10  # 表示300s内有10条写入，就产生快照
  save 60 10000
  # redis servercron 类似于linux的crontab，默认每隔100毫秒执行一次，执行持久化任务
  
  # 文件名称
  dbfilename dump.rdb
  
  # 如果持久化出错，主进程是否停止写入
  stop-writes-on-bgsave-error yes
  
  # 是否压缩
  rdbcompression yes
  
  # 导入时是否检查
  rdbchecksum yes
  
  # 文件保存路径
  dir /usr/local/redis-4.0.6
  ```

* 验证配置是否生效  

  启动redis后，在关闭redis服务，查看`/usr/local/redis-4.0.6`目录下是否生成`dump.rdb`文件


- save的含义

  ​实际生产环境每个时段的读写请求肯定不是均衡的，为此redis提供一种根据key单位时间操作次数来触发一次备份到磁盘，我们可以自由定制什么情况下触发备份，此功能起到平衡性能与数据安全的作用

- 在Redis中RDB持久化的触发分为两种：自己手动触发与Redis定时触发

  * **针对RDB方式的持久化，手动触发可以使用：**
    - save：会阻塞当前Redis服务器，直到持久化完成，线上应该禁止使用。
    - bgsave：该触发方式会fork一个子进程，由子进程负责持久化过程，因此阻塞只会发生在fork子进程的时候

![bgsave过程](https://volc1612.gitee.io/blog/images/redis缓存持久化策略/Redis持久化BGSAVE执行过程.jpg)

- 而自动触发的场景主要是有以下几点

  * 根据我们的 `save m n` 配置规则自动触发
  * 从节点全量复制时，主节点发送rdb文件给从节点完成复制操作，主节点会触发 `bgsave`
  * 执行 `debug reload` 时
  * 执行 `shutdown`时，如果没有开启aof，也会触发

- 禁用RDB

  - 只需要在save的最后一行写上
    ```sh
    save 900 1
    save 300 10
    save 60 10000
    save  ""
    ```

### Redis AOF持久化原理

**简介：rdb持久化方案配置讲解，redis的开发者是怎么实现rdb的**

- AOF持久化配置

  ```shell
  # 是否开启aof
  appendonly yes
  
  # 文件名称
  appendfilename "appendonly.aof"
  
  # 同步方式
  appendfsync everysec
  
  # aof重写期间是否同步
  no-appendfsync-on-rewrite no
  
  # 重写触发配置
  auto-aof-rewrite-percentage 100
  auto-aof-rewrite-min-size 64mb
  
  # 加载aof时如果有错如何处理
  aof-load-truncated yes # yes表示如果aof尾部文件出问题，写log记录并继续执行。no表示提示写入等待修复后写入
  
  # 文件重写策略
  aof-rewrite-incremental-fsync yes
  
  ```

- appendfsync  同步模式有三种模式，一般情况下都采用 **everysec** 配置，在数据和安全里面做平衡性选择，最多损失1s的数据

  - always：把每个写命令都立即同步到aof，很慢，但是很安全

  - everysec：每秒同步一次，是折中方案

  - no：redis不处理交给OS来处理，非常快，但是也最不安全

- AOF的整个流程大体来看可以分为两步，一步是命令的实时写入（如果是 `appendfsync everysec` 配置，会有1s损耗），第二步是对aof文件的重写。

  * 步骤：

    * 命令写入=》追加到aof_buf =》通过时间事件调用flushAppendOnlyFile函数同步到aof磁盘

  * 原因：

    * 实时写入磁盘会带来非常高的磁盘IO，影响整体性能

- AOF持久化的效率和安全性分析

  ```java
  always：每个时间事件循环都将AOF_BUF缓冲区的所有内容写入到AOF文件，并且同步AOF文件，这是最安全的方式，但磁盘操作和阻塞延迟，是IO开支较大。
  everysec：每秒同步一次，性能和安全都比较中庸的方式，也是redis推荐的方式。如果遇到物理服务器故障，有可能导致最近一秒内aof记录丢失(可能为部分丢失)。
  no：redis并不直接调用文件同步，而是交给操作系统来处理，操作系统可以根据buffer填充情况/通道空闲时间等择机触发同步；这是一种普通的文件操作方式。性能较好，在物理服务器故障时，数据丢失量会因OS配置有关。处于no模式下的flushAppendOnlyFile调用无须执行同步操作
  ```

### Redis两种持久化方案对比

![Redis数据故障恢复](https://volc1612.gitee.io/blog/images/redis缓存持久化策略/Redis数据故障恢复.jpg)

**简介：rdb和aof两种持久化方案的优缺点分析，以及如何选择**

- Redis提供了不同的持久性选项：

  - RDB持久性以指定的时间间隔执行数据集的时间点快照。
  - AOF持久性记录服务器接收的每个写入操作，将在服务器启动时再次播放，重建原始数据集。使用与Redis协议本身相同的格式以仅追加方式记录命令。当Redis太大时，Redis能够重写日志背景。

- RDB的优缺点

  * 优点：
    * RDB最大限度地提高了Redis的性能，父进程不需要参与磁盘I/O
    * 与AOF相比，RDB允许使用大数据集更快地重启
  * 缺点：
    * 如果您需要在Redis停止工作时（例如断电后）将数据丢失的可能性降至最低，则RDB并不好
    * RDB经常需要fork（）才能使用子进程持久存储在磁盘上。如果数据集很大，Fork（）可能会非常耗时

- AOF的优缺点

  * 优点：

    * 数据更加安全
    * 当Redis AOF文件太大时，Redis能够在后台自动重写AOF        ## INCRE  1  执行10万 =    INCREBY10万执行一次
    * AOF以易于理解和解析的格式一个接一个地包含所有操作的日志     # flushdb类似于rm -rf /*

  * 缺点：

    * AOF文件通常比同一数据集的等效RDB文件大

    * 根据确切的fsync策略，AOF可能比RDB慢

- RDB 和 AOF ,我应该用哪一个？
  一般来说,如果想达到足以媲美 PostgreSQL 的数据安全性， 你应该同时使用两种持久化功能。如果你非常关心你的数据,但仍然可以承受数分钟以内的数据丢失， 那么你可以只使用 RDB 持久化。有很多用户都只使用 AOF 持久化， 但我们并不推荐这种方式： 因为定时生成 RDB 快照（snapshot）非常便于进行数据库备份， 并且 RDB 恢复数据集的速度也要比 AOF 恢复的速度要快


* 在线上我们到底该怎么做？

  * RDB持久化与AOF持久化同步使用

  * 如果Redis中的数据并不是特别敏感或者可以通过其它方式重写生成数据，可以关闭持久化，如果丢失数据可以通过其它途径补回；

  * 自己制定策略定期检查Redis的情况，然后可以手动触发备份、重写数据；

  * 采用集群和主从同步

### Redis过期key清除策略

**简介：redis过期key清除策略分析**

- Redis如何淘汰过期的keys：`set name daniel 3600`

  - 惰性删除：

    * 概念：当一些客户端尝试访问它时，key会被发现并主动的过期

    * 放任键过期不管，但是每次从键空间中获取键时，都检查取得的键是否过期，如果过期的话，就删除该键

    * 特点：**CPU友好**，但如果一个key不再使用，那么它会一直存在于内存中，造成浪费

  - 定时删除：

    * 概念：设置键的过期时间的同时，创建一个定时器（timer），让定时器在键的过期时间来临时，立即执行对键的删除操作  

  - 定期删除：

    * 隔一段时间，程序就对数据库进行一次检查，删除里面的过期键，至于要删除多少过期键，

      以及要检查多少个数据库，则由算法决定。 即设置一个定时任务，比如10分钟删除一次过期的key；间隔小则占用CPU,间隔大则浪费内存

    * 例如Redis每秒处理：

    1. 测试随机的20个keys进行相关过期检测。
    2. 删除所有已经过期的keys。
    3. 如果有多于25%的keys过期，重复步奏1.

Redis服务器实际使用的是惰性删除和定期删除两种策略：通过配合使用这两种删除策略，服务器可以很好地在合理使用CPU时间和避免浪费内存空间之间取得平衡。

惰性删除策略是怎么实现？ 通过`expireIfNeeded`函数，当我们操作key的时候进行判断key是否过期
定期删除策略是怎么实现？ 通过`activeExpireCycle`函数，`serverCron`函数执行时，`activeExpireCycle`函数就会被调用，规定的时间里面分多次遍历服务器的`expires`字典随机检查一部分key的过期时间，并删除其中的过期key  