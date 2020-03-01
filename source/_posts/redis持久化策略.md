---
title: redis缓存持久化策略
date: 2020-03-01 17:26:47
update: 2020-03-01 17:26:47
categories: Redis
tags: [redis]
---

<div style="text-align: center"><iframe height="60" width="260" src="https://www.ximalaya.com/thirdparty/player/sound/player.html?id=173837050&type=red" frameborder=0 allowfullscreen></iframe></div>

<img style="width: 80%;height:80%" src="https://volc1612.gitee.io/blog/images/redis缓存持久化策略/universe.jfif" />

<!-- more -->

### Redis RDB持久化原理

**简介：rdb持久化方案配置讲解，redis的开发者是怎么实现rdb的**

- rdb持久化配置

  ```shell
  # 时间策略，表示900s内如果有1条是写入命令，就触发产生一次快照，可以理解为就进行一次备份
  save 900 1
  save 300 10  # 表示300s内有10条写入，就产生快照
  save 60 10000
  # redis servercron 类似于linux的crontab，默认每隔100毫秒执行一次
  
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

- save的含义

  ​实际生产环境每个时段的读写请求肯定不是均衡的，为此redis提供一种根据key单位时间操作次数来触发一次备份到磁盘，我们可以自由定制什么情况下触发备份，此功能起到平衡性能与数据安全的作用

- 在Redis中RDB持久化的触发分为两种：自己手动触发与Redis定时触发

  * **针对RDB方式的持久化，手动触发可以使用：**
    - save：会阻塞当前Redis服务器，直到持久化完成，线上应该禁止使用。
    - bgsave：该触发方式会fork一个子进程，由子进程负责持久化过程，因此阻塞只会发生在fork子进程的时候

- 而自动触发的场景主要是有以下几点

  * 根据我们的 `save m n` 配置规则自动触发
  * 从节点全量复制时，主节点发送rdb文件给从节点完成复制操作，主节点会触发 `bgsave`
  * 执行 `debug reload` 时
  * 执行 `shutdown`时，如果没有开启aof，也会触发

- 禁用RDB

  - 只需要在save的最后一行写上
    ```
    save  ""
    ```