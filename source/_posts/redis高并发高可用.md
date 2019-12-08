---
title: redis高并发高可用
date: 2019-11-26 21:26:47
update: 2019-11-26 21:26:47
categories: Redis
tags: [redis]
---

<div style="text-align: center"><iframe height="230" width="260" src="https://www.ximalaya.com/thirdparty/player/sound/player.html?id=173837050&type=red" frameborder=0 allowfullscreen></iframe></div>


<img style="width: 60%;height:60%" src="https://volc1612.gitee.io/blog/images/redis%E9%AB%98%E5%B9%B6%E5%8F%91%E9%AB%98%E5%8F%AF%E7%94%A8/blackLab.jpg" />

<!-- more -->

### 分布式锁

* 分布式锁是什么

* * 分布式锁是控制分布式系统或不同系统之间共同访问共享资源的一种锁实现

  * 如果不同的系统或同一个系统的不同主机之间共享了某个资源时，往往通过互斥来防止彼此干扰。

* 分布锁设计目的

​         可以保证在分布式部署的应用集群中，同一个方法在同一操作只能被一台机器上的一个线程执行。

* 设计要求
  * 这把锁要是一把可重入锁（避免死锁）
  * 这把锁有高可用的获取锁和释放锁功能
  * 这把锁获取锁和释放锁的性能要好… 

* 分布锁实现方案分析    
  * 获取锁的时候，使用 setnx(SETNX key val:当且仅当 key 不存在时，set 一个 key 为 val 的字符串，返回 1;
  * 若 key 存在，则什么都不做，返回 【0】加锁，锁的 value 值为当前占有锁服务器内网IP编号拼接任务标识
  * 在释放锁的时候进行判断。并使用 expire 命令为锁添 加一个超时时间，超过该时间则自动释放锁。 
  * 返回1则成功获取锁。还设置一个获取的超时时间， 若超过这个时间则放弃获取锁。setex（key,value,expire）过期以秒为单位
  * 释放锁的时候，判断是不是该锁（即Value为当前服务器内网IP编号拼接任务标识），若是该锁，则执行 delete 进行锁释放

### TCP三次握⼿

![redis高并发高可用-a](https://volc1612.gitee.io/blog/images/redis高并发高可用/redis高并发高可用-a.jpg)


首先Client端发送连接请求报文，Server段接受连接后回复ACK（响应）报文，并为这次连接分配资源。Client端接收到ACK（响应）报文后也向Server段发生ACK（响应）报文，并分配资源，这样TCP连接就建立了。

#### 为什么要三次握⼿，不能像http或者UDP⼀样直接传输

容易出现报文错乱。
主要是为了防⽌已失效的连接请求报⽂段突然⼜传到了B,因⽽报⽂错乱问题。假定A发出的第⼀个连
接请求报⽂段并没有丢失，⽽是在某些⽹络节点⻓时间滞留了，⼀直延迟到连接释放 以后的某个
时间才到达B，本来这是⼀个早已失效的报⽂段。但B收到此失效的连接请求报⽂段后，就误认为
是A⼜发出⼀次新的连接请求，于是就向A发出确认报⽂段，同意建⽴连接。假定不采⽤三次握
⼿，那么只要B发出确认，新的连接就建⽴了，这样⼀直等待A发来数据，B的许多资源就这样⽩⽩
浪费了。

#### 查询当前机器的连接数

```sh
[root@localhost xdclass-mobile-redis]# netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
ESTABLISHED 17
```

### TCP四次挥手

四次挥手用来断开连接

![redis高并发高可用-b](https://volc1612.gitee.io/blog/images/redis高并发高可用/redis高并发高可用-b.jpg)

* 面试题：你知道TCP四次挥手是什么吗？为什么要进行四次挥手

  * 确保数据能够完整传输

  * * 当被动方收到主动方的FIN报文通知时，它仅仅表示主动方没有数据再发送给被动方了。

    * 但未必被动方所有的数据都完整的发送给了主动方，所以被动方不会马上关闭SOCKET,它可能还需要发送一些数据给主动方后，再发送FIN报文给主动方，告诉主动方同意关闭连接
    * 所以这里的ACK报文和FIN报文多数情况下都是分开发送的。

  * 模拟流程


        A:“喂，我不说了 (FIN)。”A->FIN_WAIT1
        
        B:“我知道了(ACK)。等下，上一句还没说完。Balabala…..（传输数据）”B->CLOSE_WAIT | A->FIN_WAIT2
        
        B:”好了，说完了，我也不说了（FIN）。”B->LAST_ACK
        
        A:”我知道了（ACK）。”A->TIME_WAIT | B->CLOSED
        
        A等待2MSL,保证B收到了消息,否则重说一次”我知道了”,A->CLOSED


* 图解分析TCP四次挥手协议

  * TCP前面10种状态切换

    

* TCP第11种状态CLOSING 状态概念

  * 这种状态在实际情况中应该很少见，属于一种比较罕见的例外状态。正常情况下，当一方发送FIN报文后，按理来说是应该先收到（或同时收到）对方的ACK报文，再收到对方的FIN报文。但是CLOSING 状态表示一方发送FIN报文后，并没有收到对方的ACK报文，反而却也收到了对方的FIN报文。什么情况下会出现此种情况呢？那就是当双方几乎在同时close()一个SOCKET的话，就出现了双方同时发送FIN报文的情况，这是就会出现CLOSING 状态，表示双方都正在关闭SOCKET连接。

* netstat -anp|grep 8080


**总结**
tcp共有11中状态

### 模拟不同服务获取锁

`setnx`和`setex`获取锁操作

```java
import com.xdclass.mobile.xdclassmobileredis.RedisService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Service;

import java.net.Inet4Address;
import java.net.InetAddress;
import java.net.NetworkInterface;
import java.util.Enumeration;


@Service
public class LockNxExJob {

    private static final Logger logger = LoggerFactory.getLogger(LockNxExJob.class);

    @Autowired
    private RedisService redisService;
    @Autowired
    private RedisTemplate redisTemplate;

    private static String LOCK_PREFIX = "prefix_";


    @Scheduled(cron = "0/10 * * * * *")
    public void lockJob() {
        String lock = LOCK_PREFIX + "LockNxExJob";
        boolean nxRet = false;
        try {

            //redistemplate setnx操作
            nxRet = redisTemplate.opsForValue().setIfAbsent(lock, getHostIp());
            Object lockValue = redisService.get(lock);

            // 获取锁失败
            if (!nxRet) {
                String value = (String) redisService.get(lock);
                // 打印当前占用锁的服务器IP
                logger.info("get lock fail,lock belong to:{}", value);
                return;
                // 获取锁成功，设置锁的超时时间
            } else {
                redisTemplate.opsForValue().set(lock, getHostIp(), 3600);
                //获取锁成功
                logger.info("start lock lockNxExJob success");
                Thread.sleep(5000);
            }
        } catch (Exception e) {
            logger.error("lock error", e);

        } finally {
            if (nxRet) {
                logger.info("release lock success");
                redisService.remove(lock);
            }
        }
    }

    /**
     * 获取本机内网IP地址方法
     *
     * @return
     */
    private static String getHostIp() {
        try {
            Enumeration<NetworkInterface> allNetInterfaces = NetworkInterface.getNetworkInterfaces();
            while (allNetInterfaces.hasMoreElements()) {
                NetworkInterface netInterface = (NetworkInterface) allNetInterfaces.nextElement();
                Enumeration<InetAddress> addresses = netInterface.getInetAddresses();
                while (addresses.hasMoreElements()) {
                    InetAddress ip = (InetAddress) addresses.nextElement();
                    if (ip != null
                            && ip instanceof Inet4Address
                            && !ip.isLoopbackAddress() //loopback地址即本机地址，IPv4的loopback范围是127.0.0.0 ~ 127.255.255.255
                            && ip.getHostAddress().indexOf(":") == -1) {
                        return ip.getHostAddress();
                    }
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    public static void main(String[] args) {
        String localIP = "";
        try {
            localIP = getHostIp();
        } catch (Exception e) {
            e.printStackTrace();
        }
        //获取本机IP
        System.out.println(localIP);
    }
}
```

### `setnx`和`setex`分布式锁从入坑到放弃

![redis分布式锁遇到的问题和思考](https://volc1612.gitee.io/blog/images/redis高并发高可用/redis分布式锁遇到的问题和思考.jpg)

*  Redis分布锁思考，图解分布式锁setnx、setex的缺陷

  * 从Redis宕机讲解分布式锁执行的异常场景流程

  * 从Server服务宕机讲解分布式锁执行的异常场景流程

    

* 实战演练Server服务宕机情况

  

  * 先kill调集群里面的其他节点的java进程，

  *  在执行job的时候将进程kill掉

  *      通过ps -ef|grep java看到进程的pid

  * 重启服务，看服务是否每次都获取锁失败    



* 一步步分析解决问题方案

  * 怎么一次性执行过一条命令而不会出现问题，采用Lua脚本
  * Redis从`2.6`之后支持setnx、setex连用


###  Lua脚本讲解之Redis分布式锁

**Lua脚本setnx、setex命令连用**

*  Lua简介
  * 从 Redis 2.6.0 版本开始，通过内置的 Lua 解释器，可以使用 EVAL 命令对 Lua 脚本进行求值。
  * Redis 使用单个 Lua 解释器去运行所有脚本，并且， Redis 也保证脚本会以原子性(atomic)的方式执行：当某个脚本正在运行的时候，不会有其他脚本或 Redis 命令被执行。这和使用 MULTI / EXEC 包围的事务很类似。在其他别的客户端看来，脚本的效果(effect)要么是不可见的(not visible)，要么就是已完成的(already completed)。
* Lua脚本配置流程
  *  1、在resource目录下面新增一个后缀名为.lua结尾的文件
  * 2、编写lua脚本
  * 3、传入lua脚本的key和arg
  * 4、调用redisTemplate.execute方法执行脚本
* Lua脚本结合RedisTempalte实战演练
* Lua脚本其他工作场景剖析和演练
* lua eval http://doc.redisfans.com/script/eval.html

### `RedisConnection`实现分布式锁

![RedisConnection实现分布式锁](https://volc1612.gitee.io/blog/images/redis高并发高可用/RedisConnection实现分布式锁.png)

**简介：RedisConnection实现分布锁的方式，采用redisTemplate操作redisConnection
           实现setnx和setex两个命令连用**

- redisTemplate本身有没通过valueOperation实现分布式锁

  * 问题探索：
             Spring Data Redis提供了与Java客户端包的集成服务，比如Jedis, JRedis等 
             通过getNativeConnection的方式可以解决问题吗？

  

- Spring Data Redis提供了与Java客户端包的集成服务，比如Jedis, JRedis等 

  * 代码演示

  * ```java
    /**
            * 重写redisTemplate的set方法
            * <p>
            * 命令 SET resource-name anystring NX EX max-lock-time 是一种在 Redis 中实现锁的简单方法。
            * <p>
            * 客户端执行以上的命令：
            * <p>
            * 如果服务器返回 OK ，那么这个客户端获得锁。
            * 如果服务器返回 NIL ，那么客户端获取锁失败，可以在稍后再重试。
            *
            * @param key     锁的Key
            * @param value   锁里面的值
            * @param seconds 过去时间（秒）
            * @return
         */
          private String set(final String key, final String value, final long seconds) {
            Assert.isTrue(!StringUtils.isEmpty(key), "key不能为空");
            return redisTemplate.execute(new RedisCallback<String>() {
                @Override
                public String doInRedis(RedisConnection connection) throws DataAccessException {
                    Object nativeConnection = connection.getNativeConnection();
                    String result = null;
                    if (nativeConnection instanceof JedisCommands) {
                        result = ((JedisCommands) nativeConnection).set(key, value, NX, EX, seconds);
                    }
    
                    if (!StringUtils.isEmpty(lockKeyLog) && !StringUtils.isEmpty(result)) {
                        logger.info("获取锁{}的时间：{}", lockKeyLog, System.currentTimeMillis());
                    }
    
                    return result;
                }
            });
          }
    ```

    

- 为什么新版本的spring-data-redis会报class not can not be case错误
            

```
io.lettuce.core.RedisAsyncCommandsImpl cannot be cast to redis.clients.jedis.JedisCommands
```

  

- 探索spring-data-redis升级

  * 官网api分析
             https://docs.spring.io/spring-data/redis/docs/1.5.0.RELEASE/api/
             https://docs.spring.io/spring-data/redis/docs/2.0.13.RELEASE/api/

  * 源码改造

```java
public Boolean doInRedis(RedisConnection connection) throws DataAccessException {
        RedisConnection redisConnection =             redisTemplate.getConnectionFactory().getConnection();
        return redisConnection.set(key.getBytes(), getHostIp().getBytes(), Expiration.seconds(expire), RedisStringCommands.SetOption.ifAbsent());
}

```

### 解锁存在的问题和解决方法


解锁过程不是原子操作可导致服务器解锁了其他锁

![分布式锁优化分析](https://volc1612.gitee.io/blog/images/redis高并发高可用/分布式锁优化分析.jpg)

解锁的流程分析
当某个锁需要持有的时间⼩于锁超时时间时会出现两个进程同时执⾏任务的情况， 这时候如果进
程没限制只有占有这把锁的⼈才能解锁的原则就会出现， A解了B的锁。
采⽤lua脚本做解锁流程优化讲解

```lua
local lockKey = KEYS[1]
local lockValue = KEYS[2]

-- get key
local result_1 = redis.call('get', lockKey)
if result_1 == lockValue
then
    local result_2 = redis.call('del', lockKey)
    return result_2
else
    return false
end
```

```java
@Scheduled(cron = "0/10 * * * * *")
public void lockJob() {

    String lock = LOCK_PREFIX + "JedisNxExJob";
    boolean lockRet = false;
    try {
        lockRet = this.setLock(lock, 600);

        //获取锁失败
        if (!lockRet) {
            String value = (String) redisService.getValue(lock);
            //打印当前占用锁的服务器IP
            logger.info("jedisLockJob get lock fail,lock belong to:{}", value);
            return;
        } else {
            //获取锁成功
            logger.info("jedisLockJob start  lock lockNxExJob success");
            Thread.sleep(5000);
        }
    } catch (Exception e) {
        logger.error("jedisLockJob lock error", e);

    } finally {
        if (lockRet) {
            logger.info("jedisLockJob release lock success");
            releaseLock(lock, getHostIp());
        }
    }
}
```

```java
private DefaultRedisScript<Boolean> lockScript;
/**
* 释放锁操作
*
* @param key
* @param value
* @return
*/
private boolean releaseLock(String key, String value) {
    lockScript = new DefaultRedisScript<>();
    lockScript.setScriptSource(
            new ResourceScriptSource(new ClassPathResource("unlock.lua")));
    lockScript.setResultType(Boolean.class);
    // 封装参数
    List<Object> keyList = new ArrayList<>();
    keyList.add(key);
    keyList.add(value);
    Boolean result = redisTemplate.execute(lockScript, keyList);
    return result;
}
```

### 采用lua脚本做高可用分布式锁的优化


- 解锁的流程分析

  ​        当某个锁需要持有的时间小于锁超时时间时会出现两个进程同时执行任务的情况，
  ​         这时候如果进程没限制只有占有这把锁的人才能解锁的原则就会出现，
  ​         A解了B的锁。

- 采用lua脚本做解锁流程优化讲解

### 经验分享面试技巧分析之支付宝分布式锁

- 问题1：
      1、什么是分布式锁？

  * 首先，为了确保分布式锁可用，我们至少要确保锁的实现同时满足以下三个条件：
    * 互斥性。在任意时刻，只有一个客户端能持有锁。
    * 不会发生死锁。即使有一个客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁。
    * 解铃还须系铃人。加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了

- * 从 Redis 2.6.0 版本开始，通过内置的 Lua 解释器，可以使用 EVAL 命令对 Lua 脚本进行求值。

  - Redis 使用单个 Lua 解释器去运行所有脚本，并且， Redis 也保证脚本会以原子性(atomic)的方式执行：当某个脚本正在运行的时候，不会有其他脚本或 Redis 命令被执行。这和使用 MULTI / EXEC 包围的事务很类似。在其他别的客户端看来，脚本的效果(effect)要么是不可见的(not visible)，要么就是已完成的(already completed)。

- 问题2：怎么实现分布式锁

  - 实现分布式锁的方案大概有两种
    * 采用lua脚本操作分布式锁
    * 采用setnx、setex命令连用的方式实现分布式锁

- 问题3：

  * 解锁需要注意什么

      解铃还须系铃人。加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了



### 你知道Redis读写分离是怎么做数据同步的

- redis2.8版本之前使用旧版复制功能SYNC

  * SYNC是一个非常耗费资源的操作
    * 主服务器需要执行BGSAVE命令来生成RDB文件，这个生成操作会耗费主服务器大量的的CPU、内存和磁盘读写资源
    * 主服务器将RDB文件发送给从服务器，这个发送操作会耗费主从服务器大量的网络带宽和流量，并对主服务器响应命令
    * 请求的时间产生影响：接收到RDB文件的从服务器在载入文件的过程是阻塞的，无法处理命令请求




- 2.8之后使用PSYNC

  - PSYNC命令具有完整重同步（full resynchronization）和部分重同步（partial resynchronization1）两种模式

    *  部分重同步功能由以下三个部分构成：
      * 主服务的复制偏移量（replication offset）和从服务器的复制偏移量
      * 主服务器的复制积压缓冲区（replication backlog）,默认大小为1M
      * 服务器的运行ID(run ID),用于存储服务器标识，如从服务器断线重新连接，取到主服务器的运行ID与重接后的主服务器运行ID进行对比，从而判断是执行部分重同步还是执行完整重同步


![redis读写分离PSYNC](https://volc1612.gitee.io/blog/images/redis高并发高可用/redis读写分离PSYNC.png)      

![redis读写分离重同步](https://volc1612.gitee.io/blog/images/redis高并发高可用/redis读写分离重同步.png)

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

![redis高可用之哨兵模式](https://volc1612.gitee.io/blog/images/redis高并发高可用/redis高可用之哨兵模式.png)