---
title: Redis
date: 2017-06-02 12:59:16
update: 2019-11-04 12:59:16
categories: Redis
tags: [redis]
---

Redis特性
![texing](https://volc1612.gitee.io/blog/images/Redis/texing.png)
<!-- more -->
### 1 windows(64)环境下解压安装包redis-2.8.9.zip
感兴趣的点击 ->> <a href="http://volc1612.gitee.io/blog/2017/03/05/linux%E4%B8%8B%E5%AE%89%E8%A3%85Redis/">linux下安装Redis</a>
#### 1.1 删除Redis服务
管理员命令
```
C:\windows\system32>sc delete Redis
[SC] DeleteService 成功

C:\windows\system32>
```
#### 1.2 双击redis-server.exe

![redis](https://volc1612.gitee.io/blog/images/Redis/redis.png)

#### 1.3 注册到系统服务中
执行以下命令即可
```
D:\redis-2.8.9>redis-server --service-install redis.windows.conf --loglevel verbose
[13828] 02 Jun 14:17:18.824 # Redis successfully installed as a service.
D:\redis-2.8.9>
```
找到Redis服务并启动

#### 1.4 测试

双击D:\redis-2.8.9\redis-cli.exe

```
127.0.0.1:6379> ping
PONG  -- 成功
127.0.0.1:6379>
```
#### 1.5 优化内存
修改D:\redis-2.8.9\redis.windows.conf文件
```
# maxmemory <bytes>
maxmemory 200mb          # 200M
```
重启Redis服务,可以看到D:\redis-2.8.9>\RedisQFork_1004.dat这个文件大小控制在300M左右，节省了内存资源。

#### 1.5 总结
可连接外部服务器上的Redis
![cl](https://volc1612.gitee.io/blog/images/Redis/cl.png)

### 2 redis-cli的使用之命令返回值
![cl](https://volc1612.gitee.io/blog/images/Redis/re.png)

### 3 多数据库
![many](https://volc1612.gitee.io/blog/images/Redis/many.png)

FLUSHALL  -- 清空所有数据库的所有数据
FLUSHDB   --  清空当前所在数据库的数据
### 4 Redis的基本命令
#### 4.1 KEYS
![cl](https://volc1612.gitee.io/blog/images/Redis/p1.png)
#### 4.2 EXISTS
![cl](https://volc1612.gitee.io/blog/images/Redis/p2.png)
#### 4.3 DEL
![cl](https://volc1612.gitee.io/blog/images/Redis/p3.png)
删除key为a、b、c、d的数据
```
key a b c d
```
#### 4.4 TYPE
![p4](https://volc1612.gitee.io/blog/images/Redis/p4.png)
#### 4.5 HELP
HELP 空格 tab键
![cl](https://volc1612.gitee.io/blog/images/Redis/p5.png)

### 5 Redis的字符串数据类型
#### 5.1 字符串类型
![d1](https://volc1612.gitee.io/blog/images/Redis/d1.png)
#### 5.2 GET、SET
![d1](https://volc1612.gitee.io/blog/images/Redis/d2.png)
#### 5.3 INCR
![d1](https://volc1612.gitee.io/blog/images/Redis/d3.png)
#### 5.3 INCRBY
![d1](https://volc1612.gitee.io/blog/images/Redis/d4.png)
#### 5.3 DECR、DECRBY
![d1](https://volc1612.gitee.io/blog/images/Redis/d5.png)
#### 5.3 APPEND
![d1](https://volc1612.gitee.io/blog/images/Redis/d6.png)
#### 5.3 STRLEN
![d1](https://volc1612.gitee.io/blog/images/Redis/d7.png)
#### 5.3 MSET、MGET
![d1](https://volc1612.gitee.io/blog/images/Redis/d8.png)

### 6 Redis之生存时间(用于缓存)
#### 6.1 设置生存时间
![s](https://volc1612.gitee.io/blog/images/Redis/s1.png)
TTL返回值：
大于0的数字：剩余生存时间，单位为秒
-1 ： 没有生存时间，永久存储
-2 ： 数据已经被删除
#### 6.1 清除生存时间
![s](https://volc1612.gitee.io/blog/images/Redis/s2.png)
生存时间是针对值的，并不是key。
#### 6.2 设置毫秒
![s](https://volc1612.gitee.io/blog/images/Redis/s3.png)
### 7 spring整合Redis
#### 7.1 引用依赖
```
<dependency>
	<groupId>redis.clients</groupId>
	<artifactId>jedis</artifactId>
	<version>2.6.0</version>
</dependency>
```
使用Redis要确定一点是，缓存逻辑要写在后台。

导入依赖的原则：
1、在使用依赖的最底层导入。Redis业务是在聚合工程中的service项目中，所以要在service项目中引入依赖
2、运行时所需要的依赖在(聚合工程中的)web项目中加入。(系统加载时就需要依赖)
3、所有的工程都需要的依赖应该在聚合工程（taotao-manage）中导入。

#### 7.2 spring整合Redis文件
applicationContext-redis.xml(聚合工程web项目中)
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd">
	<!-- 定义分片式连接池 -->
	<bean id="shardedJedisPool" class="redis.clients.jedis.ShardedJedisPool">
		<constructor-arg index="0"> <!-- 第一个参数 -->
			<bean class="redis.clients.jedis.JedisPoolConfig">
				<property name="maxTotal" value="${redis.maxTotal}"></property>
			</bean>
		</constructor-arg>
		<constructor-arg index="1"> <!-- 第二个参数 -->
			<list><!-- 可定义多个redis节点分片式集群 -->
				<bean class="redis.clients.jedis.JedisShardInfo">
					<constructor-arg index="0" value="${redis.node1.host}"></constructor-arg>
					<constructor-arg index="1" value="${redis.node1.port}"></constructor-arg>
				</bean>
			</list>
		</constructor-arg>	
	</bean>
	<!-- 结束 -->
</beans>
```
#### 7.3 Redis配置属性文件
redis.properties((聚合工程web项目中))
```xml
redis.maxTotal=100
redis.node1.host=127.0.0.1
redis.node1.port=6379
```
#### 7.4 封装RedisService类
处理代码复用的问题，面向接口编程。
公用的代码逻辑放在同一个方法中，而不同的逻辑需要具体实现。这里就要使用接口处理。

接口类
```java
public interface Function<T, E> {
    public T callback(E e); // 具体的业务实现方法
}
```
RedisService类
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import redis.clients.jedis.ShardedJedis;
import redis.clients.jedis.ShardedJedisPool;

/**
 * @ClassName RedisService
 * @Author volc
 * @Description 封住用于redis缓存的数据操作，通用方法
 * @Date 2017年2月27日 下午8:42:36
 */
@Service
public class RedisService {

    // 保证common的通用性，如果spring容器中没有ShardedJedisPool bean就不使用redis如果有就使用
    @Autowired(required = false)
    private ShardedJedisPool shardedJedisPool;

    /**
     * @MethodName execute 
     * @Author volc
     * @Description 泛型方法用于处理存入缓存和从缓存中取数据
     * @Date 2017年2月27日 下午9:04:59
     */
    private <T> T execute(Function<T, ShardedJedis> fun) {
	ShardedJedis shardedJedis = null;
	try {
	    // 从连接池中获取到jedis分片对象
	    shardedJedis = shardedJedisPool.getResource();
	    // 数据放入到redis中
	    return fun.callback(shardedJedis); // insert成功返回OK
	} catch (Exception e) {
	    e.printStackTrace();
	} finally {
	    if (null != shardedJedis) {
		// 关闭，检测连接是否有效，有效则放回到连接池中，无效则重置状态
		shardedJedis.close();
	    }
	}
	return null;
    }

    /**
     * @MethodName set 
     * @Author volc
     * @Description 缓存数据到redis
     * @Date 2017年2月27日 下午8:45:51
     */
    public String set(final String key, final String value) {
	return this.execute(new Function<String, ShardedJedis>() { // 匿名实现类存在作用域，不能使用非final参数
	    @Override
	    public String callback(ShardedJedis e) {
		return e.set(key, value); // 添加成功返回OK
	    }
	});
	/*
	 * ShardedJedis shardedJedis = null; try { // 从连接池中获取到jedis分片对象
	 * shardedJedis = shardedJedisPool.getResource(); // 数据放入到redis中 return
	 * shardedJedis.set(key, value); // insert成功返回OK } catch (Exception e) {
	 * e.printStackTrace(); } finally { if (null != shardedJedis) { //
	 * 关闭，检测连接是否有效，有效则放回到连接池中，无效则重置状态 shardedJedis.close(); } } return null;
	 */
    }

    /**
     * @MethodName get 
     * @Author volc
     * @Description 从redis缓存中取数据
     * @Date 2017年2月27日 下午8:48:47
     */
    public String get(final String key) {
	return this.execute(new Function<String, ShardedJedis>() {
	    @Override
	    public String callback(ShardedJedis e) {
		return e.get(key); // 返回key对应的value值
	    }
	});
	/*
	 * ShardedJedis shardedJedis = null; try { // 从连接池中获取到jedis分片对象
	 * shardedJedis = shardedJedisPool.getResource(); // 从redis中取数据 return
	 * shardedJedis.get(key); // 返回key对应的value值 } catch (Exception e) {
	 * e.printStackTrace(); } finally { if (null != shardedJedis) { //
	 * 关闭，检测连接是否有效，有效则放回到连接池中，无效则重置状态 shardedJedis.close(); } } return null;
	 */
    }

    /**
     * @MethodName del
     * @Author volc
     * @Description 根据key删除缓存
     * @Date 2017年2月27日 下午9:21:09
     */
    public Long del(final String key) {
	return this.execute(new Function<Long, ShardedJedis>() {
	    @Override
	    public Long callback(ShardedJedis e) {
		return e.del(key); // 根据key删除redis缓存
	    }
	});
    }

    /**
     * @MethodName expire
     * @Author volc
     * @Description 设置缓存的生存时间，到期会从缓存中移除
     * @Date 2017年2月27日 下午9:24:18
     */
    public Long expire(final String key, final Integer seconds) {
	return this.execute(new Function<Long, ShardedJedis>() {
	    @Override
	    public Long callback(ShardedJedis e) {
		return e.expire(key, seconds);
	    }
	});
    }

    /**
     * @MethodName set
     * @Author volc
     * @Description 设置String类型的值，存入缓存时设置缓存数据的生存时间
     * @Date 2017年2月27日 下午9:26:10
     */
    public String set(final String key, final String value, final Integer seconds) {
	return this.execute(new Function<String, ShardedJedis>() {
	    @Override
	    public String callback(ShardedJedis e) {
		String result = e.set(key, value);
		// 设置这个缓存数据的生存时间
		e.expire(key, seconds);
		return result;
	    }
	});
    }
}

```
以上的方法是Redis缓存通用方法，需要放到common项目中，以便其他项目调用。

#### 7.5 项目添加缓存逻辑
**原则：不能影响原来的业务逻辑的正常执行**，通常使用trycatch处理。
目的：提高查询速度，减少数据库压力
项目中添加缓存的逻辑：
前后台交互：
	查询之前先从缓存中命中，如果命中就返回。没有命中才去数据库中查询(程序继续执行)，再把数据存放到缓存中。
后台：
	问题：当后台改变数据时，应该通知前台数据改变了，否则前台查出来的数据仍是缓存中未改变的数据。
	实现：前台系统应该为后台提供一个删除该缓存的接口，当后台系统改变数据时，调用前台的这个接口，删除该缓存，保证数据的准确性。
	注意：
		>1.service层中不能使用trycatch，一旦捕获异常，事物将会失效。
		>2.而后台请求前台的删除缓存接口逻辑和后台的其他逻辑不属于同一业务，这里是可以进行trycatch处理的。
		>3.这一机制没有考虑到系统之间的耦合度，也就是前台需求改变，后台也要去跟着改变逻辑适应前台。
		>4.以上是临时解决方法，可采用rabbitmq(消息队列)代替，实现业务并降低系统之间的耦合度。
		  

部分代码：

接受json数据的bean
ItemCatResult
```java

import java.util.ArrayList;
import java.util.List;
import com.fasterxml.jackson.annotation.JsonProperty;

public class ItemCatResult {

    @JsonProperty("data") // 指定序列化json数据后的名称
    private List<ItemCatData> itemCats = new ArrayList<ItemCatData>();

    public List<ItemCatData> getItemCats() {
	return itemCats;
    }

    public void setItemCats(List<ItemCatData> itemCats) {
	this.itemCats = itemCats;
    }
}
```
ItemCatData
```java

import java.util.List;
import com.fasterxml.jackson.annotation.JsonProperty;

public class ItemCatData {

    @JsonProperty("u") // 序列化成json数据时为 u
    private String url;

    @JsonProperty("n")
    private String name;

    @JsonProperty("i")
    private List<?> items;

    public String getUrl() {
	return url;
    }

    public void setUrl(String url) {
	this.url = url;
    }

    public String getName() {
	return name;
    }

    public void setName(String name) {
	this.name = name;
    }

    public List<?> getItems() {
	return items;
    }

    public void setItems(List<?> items) {
	this.items = items;
    }

}

```
访问接口的逻辑处理类ItemCatService
```java

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import org.apache.commons.lang3.StringUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.taotao.common.bean.ItemCatData;
import com.taotao.common.bean.ItemCatResult;
import com.taotao.common.service.RedisService;
import com.taotao.manage.pojo.ItemCat;

@Service
public class ItemCatService extends BaseService<ItemCat> {

    @Autowired
    private RedisService redisService;

    private final ObjectMapper MAPPER = new ObjectMapper();

    private static final String REDIS_KEY = "TAOTAO_MANAGE_ITEM_CAT_ALL"; // 最佳实践，项目名+模块名+业务名

    private static final Integer REDIS_TIME = 60 * 60 * 24 * 30 * 3;

    /**
     * 全部查询，并且生成树状结构
     * @return
     */
    public ItemCatResult queryAllToTree() {
	ItemCatResult result = new ItemCatResult();
	/*
	 * 先从缓存中命中，如果命中就返回，如果没有命中执行以下程序 ，这里加入缓存有可能影响业务逻辑的正常执行，这里需要进行优化处理
	 * 进行trycatch，出现异常捕获后继续向下进行
	 */
	try {
	    String cacheData = this.redisService.get(REDIS_KEY);
	    /**
	     * cacheData格式
	        {
	          "data": [
	            {
	              "u": "/products/1.html",
	              "n": "<a href='/products/1.html'>图书、音像、电子书刊</a>",
	              "i": [
	                {
	                  "u": "/products/2.html",
	                  "n": "电子书刊",
	                  "i": [
	                    "/products/3.html|电子书",
	                    "/products/4.html|网络原创",
	                    "/products/5.html|数字杂志",
	                    "/products/6.html|多媒体图书"
	                  ]
	                },
	                {
	                  "u": "/products/7.html",
	                  "n": "音像",
	                  "i": [
	                    "/products/8.html|音乐",
	                    "/products/9.html|影视",
	                    "/products/10.html|教育音像"
	                  ]
	                }
	              ]
	            }
	          ]
	        }
	     */
	    if (StringUtils.isNotEmpty(cacheData)) {
		// 命中,进行反序列化，ItemCatData中items是个泛型的list，并不会转成具体的对象
		return MAPPER.readValue(cacheData, ItemCatResult.class);
	    }
	} catch (Exception e) {
	    e.printStackTrace();
	}

	// 全部查出，并且在内存中生成树形结构
	List<ItemCat> cats = super.queryAll();
	// 转为map存储，key为父节点ID，value为数据集合
	Map<Long, List<ItemCat>> itemCatMap = new HashMap<Long, List<ItemCat>>();
	for (ItemCat itemCat : cats) {
	    if (!itemCatMap.containsKey(itemCat.getParentId())) {
		itemCatMap.put(itemCat.getParentId(), new ArrayList<ItemCat>());
	    }
	    itemCatMap.get(itemCat.getParentId()).add(itemCat);
	}

	// 封装一级对象
	List<ItemCat> itemCatList1 = itemCatMap.get(0L);
	for (ItemCat itemCat : itemCatList1) {
	    ItemCatData itemCatData = new ItemCatData();
	    itemCatData.setUrl("/products/" + itemCat.getId() + ".html");
	    itemCatData.setName("<a href='" + itemCatData.getUrl() + "'>" + itemCat.getName() + "</a>");
	    result.getItemCats().add(itemCatData);
	    if (!itemCat.getIsParent()) {
		continue;
	    }

	    // 封装二级对象
	    List<ItemCat> itemCatList2 = itemCatMap.get(itemCat.getId());
	    List<ItemCatData> itemCatData2 = new ArrayList<ItemCatData>();
	    itemCatData.setItems(itemCatData2);
	    for (ItemCat itemCat2 : itemCatList2) {
		ItemCatData id2 = new ItemCatData();
		id2.setName(itemCat2.getName());
		id2.setUrl("/products/" + itemCat2.getId() + ".html");
		itemCatData2.add(id2);
		if (itemCat2.getIsParent()) {
		    // 封装三级对象
		    List<ItemCat> itemCatList3 = itemCatMap.get(itemCat2.getId());
		    List<String> itemCatData3 = new ArrayList<String>();
		    id2.setItems(itemCatData3);
		    for (ItemCat itemCat3 : itemCatList3) {
			itemCatData3.add("/products/" + itemCat3.getId() + ".html|" + itemCat3.getName());
		    }
		}
	    }
	    if (result.getItemCats().size() >= 14) {
		break;
	    }
	}

	/*
	 * 这里不能用JsonProcessingException进行捕获，需要使用Exception大的异常进行获取添加缓存时的异常
	 */
	try {
	    // 将结果集写入到缓存中
	    this.redisService.set(REDIS_KEY, MAPPER.writeValueAsString(result), REDIS_TIME);
	} catch (Exception e) {
	    e.printStackTrace();
	}
	return result;
    }

}

```

### 8 linux安装Redis
![anzhaung](https://volc1612.gitee.io/blog/images/Redis/anzhuang.png)
注意：需要开放6379端口

### 9 缓存三暖男
ehcache memcache redis
http://www.cnblogs.com/duanxz/p/5215274.html

关于redis和memcache的不同，下面罗列了一些相关说法，供记录：
    redis和memecache的不同在于[2]：
    1、存储方式：
    memecache 把数据全部存在内存之中，断电后会挂掉，数据不能超过内存大小
    redis有部份存在硬盘上，这样能保证数据的持久性，支持数据的持久化（笔者注：有快照和AOF日志两种持久化方式，在实际应用的时候，要特别注意配置文件快照参数，要不就很有可能服务器频繁满载做dump）。
    2、数据支持类型：
    redis在数据支持上要比memecache多的多。
    3、使用底层模型不同：
    新版本的redis直接自己构建了VM 机制 ，因为一般的系统调用系统函数的话，会浪费一定的时间去移动和请求。
    4、运行环境不同：
    redis目前官方只支持LINUX 上去行，从而省去了对于其它系统的支持，这样的话可以更好的把精力用于本系统 环境上的优化，虽然后来微软有一个小组为其写了补丁。但是没有放到主干上
个人总结一下，有持久化需求或者对数据结构和处理有高级要求的应用，选择redis，其他简单的key/value存储，选择memcache。

关系型与非关系型数据库
https://www.zhihu.com/question/24225007

### redis数据类型
#### String
```
redis 127.0.0.1:6379> SET name "runoob"
OK
redis 127.0.0.1:6379> GET name
"runoob"
```

#### Hash（哈希）
Redis hash 是一个键值(key=>value)对集合。
Redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象。

```
redis> HMSET myhash field1 "Hello" field2 "World"
"OK"
redis> HGET myhash field1
"Hello"
redis> HGET myhash field2
"World"
```

实例中我们使用了 Redis HMSET, HGET 命令，HMSET 设置了两个 field=>value 对, HGET 获取对应 field 对应的 value。
每个 hash 可以存储 232 -1 键值对（40多亿）。

#### List（列表）

Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。

```
redis 127.0.0.1:6379> lpush runoob redis
(integer) 1
redis 127.0.0.1:6379> lpush runoob mongodb
(integer) 2
redis 127.0.0.1:6379> lpush runoob rabitmq
(integer) 3
redis 127.0.0.1:6379> lrange runoob 0 10
1) "rabitmq"
2) "mongodb"
3) "redis"
redis 127.0.0.1:6379>
```

列表最多可存储 232 - 1 元素 (4294967295, 每个列表可存储40多亿)。

#### Set（集合）

```
redis 127.0.0.1:6379> sadd runoob redis
(integer) 1
redis 127.0.0.1:6379> sadd runoob mongodb
(integer) 1
redis 127.0.0.1:6379> sadd runoob rabitmq
(integer) 1
redis 127.0.0.1:6379> sadd runoob rabitmq
(integer) 0
redis 127.0.0.1:6379> smembers runoob

1) "redis"
2) "rabitmq"
3) "mongodb"
```

注意：以上实例中 rabitmq 添加了两次，但根据集合内元素的唯一性，第二次插入的元素将被忽略。

集合中最大的成员数为 232 - 1(4294967295, 每个集合可存储40多亿个成员)。

#### zset(sorted set：有序集合)

Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。
不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。
zset的成员是唯一的,但分数(score)却可以重复。

##### zadd 命令

添加元素到集合，元素在集合中存在则更新对应score
```
zadd key score member 
```
#### 实例

```
redis 127.0.0.1:6379> zadd runoob 0 redis
(integer) 1
redis 127.0.0.1:6379> zadd runoob 0 mongodb
(integer) 1
redis 127.0.0.1:6379> zadd runoob 0 rabitmq
(integer) 1
redis 127.0.0.1:6379> zadd runoob 0 rabitmq
(integer) 0
redis 127.0.0.1:6379> > ZRANGEBYSCORE runoob 0 1000
1) "mongodb"
2) "rabitmq"
3) "redis"
```


### linux编译安装redis时的坑

#### 一定要指定配置文件启动redis

```sh
[root@localhost etc]# ./redis-server /usr/local/src/redis4/redis-4.0.6/redis.conf
```

### redis开机启动服务

#### 创建启动服务脚本

复制`redis`安装目录下`utils/redis_init_script`文件到`/etc/init.d`中

#### 修改配置启动服务脚本redis_init_script

```sh
cd /etc/init.d
```
修改内容如下

##### 新增启动权限设置

```
# chkconfig: 2345 80 90
```

##### 修改配置一下配置项

`EXEC`
`CLIEXEC`
`CONF`


将`$EXEC $CONF`改成`$EXEC $CONF &`


完整脚本如下

```sh
#!/bin/sh
#
# Simple Redis init.d script conceived to work on Linux systems
# chkconfig: 2345 80 90
# as it does use of the /proc filesystem.

REDISPORT=6379
EXEC=/usr/local/src/redis4/redis-4.0.6/src/redis-server
CLIEXEC=/usr/local/src/redis4/redis-4.0.6/src/redis-cli

PIDFILE=/var/run/redis_${REDISPORT}.pid
CONF="/usr/local/src/redis4/redis-4.0.6/redis.conf"

case "$1" in
    start)
        if [ -f $PIDFILE ]
        then
            echo "$PIDFILE exists, process is already running or crashed"
        else
            echo "Starting Redis server..."
            $EXEC $CONF &
        fi
    ;;
    stop)
        if [ ! -f $PIDFILE ]
        then
            echo "$PIDFILE does not exist, process is not running"
        else
            PID=$(cat $PIDFILE)
            echo "Stopping ..."
            $CLIEXEC -p $REDISPORT shutdown
            while [ -x /proc/${PID} ]
            do
                echo "Waiting for Redis to shutdown ..."
                sleep 1
            done
            echo "Redis stopped"
        fi
    ;;
    *)
        echo "Please use start or stop as first argument"
    ;;
esac
```


#### 服务添加到chkconfig

```sh
[root@localhost init.d]# chkconfig --add redis_init_script
```

#### 给启动脚本添加权限

```sh
[root@localhost init.d]# chmod +x /etc/init.d/redis_init_script
```

#### 测试启动脚本是否生效

```sh
[root@localhost init.d]# service redis_init_script start
[root@localhost init.d]# service redis_init_script stop
```

#### 添加启动脚本到chkconfig管理

```sh
[root@localhost init.d]# chkconfig redis_init_script on
```

重启服务器

```sh
[root@localhost init.d]# reboot
```

### redis之sortset数据类型

- ./redis-cli -h 127.0.0.1 -p  6379
- zadd ——在key对应的zset中添加一个元素
- zrange——获取key对应的zset中指定范围的元素，-1表示获取所有元素
- zrem——删除key对应的zset中的一个元素
- zrangebyscore——返回有序集key中，指定分数范围的元素列表,排行榜中运用
- zrank——返回key对应的zset中指定member的排名。其中member按score值递增(从小到大）；
         排名以0为底，也就是说，score值最小的成员排名为0,排行榜中运用      
- zadd ==》zrem
-    set是通过hashmap存储，key对应set的元素，value是空对象
     sortset是怎么存储并实现排序的呢，hashmap存储，还加了一层跳跃表
     跳跃表：相当于双向链表，在其基础上添加前往比当前元素大的跳转链接 