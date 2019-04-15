---
title: RabbitMQ
date: 2017-03-05 11:15:54
update: 2017-06-03 11:15:54
categories: RabbitMQ
tags: [rabbitmq]
---

### linux安装RabbitMQ(3.7.8)新

### 编译安装Erlang

#### 安装GCC GCC-C++ Openssl等模块编译Erlang

```bash
[root@VM_0_11_redhat ~]# yum -y install make gcc gcc-c++ kernel-devel m4 ncurses-devel openssl-devel
```

#### 安装ncurses

```bash
[root@VM_0_11_redhat ~]# yum -y install ncurses-devel
```

#### 安装Erlang

```bash
[root@ip-172-31-17-64 ~]# cd /usr/local
# 下载Erlang
[root@ip-172-31-17-64 local]# wget http://erlang.org/download/otp_src_21.0.tar.gz
# 解压
[root@ip-172-31-17-64 local]# tar -xvf otp_src_21.0.tar.gz
# 重命名文件夹为erlang
[root@ip-172-31-17-64 local]# mv otp_src_21.0 ./erlang
[root@ip-172-31-17-64 local]# cd erlang
# ./configure检查编译环境并配置安装路径
[root@ip-172-31-17-64 local]# ./configure --prefix=/usr/local/erlang --without-javac
# 编码
[root@ip-172-31-17-64 local]# make
[root@ip-172-31-17-64 local]# make install
```
配置`/etc/profile`添加eralng环境变量,增加一行保存`export PATH=$PATH:/user/local/erlang/bin`

```bash
# 设置erlang环境变量添加export PATH=$PATH:/user/local/erlang/bin
[root@ip-172-31-17-64 local]# vi /etc/profile
```
```bash
# 创建软连接
[root@ip-172-31-17-64 local]# ln -s /usr/local/erlang/bin/erl /usr/local/bin/erl
# 验证是否成功
[root@ip-172-31-17-64 local]# erl
Erlang/OTP 21 [erts-10.0] [source] [64-bit] [smp:1:1] [ds:1:1:10] [async-threads:1] [hipe]

Eshell V10.0  (abort with ^G)
1> 
# 表示成功
```

### 安装rabbitmq-server

```bash
[root@ip-172-31-17-64 local]# cd /usr/local/
# 下载安装包
[root@ip-172-31-17-64 local]# wget https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.7.8/rabbitmq-server-generic-unix-3.7.8.tar.xz
# 下载xz压缩工具
[root@ip-172-31-17-64 local]# yum install xz
# 解压.xz
[root@ip-172-31-17-64 local]# xz -d rabbitmq-server-generic-unix-3.7.8.tar.xz 

# 解压.tar
[root@ip-172-31-17-64 local]# tar -xvf rabbitmq-server-generic-unix-3.7.8.tar
# 重命名
[root@ip-172-31-17-64 local]# mv rabbitmq_server-3.7.8 rabbitmq
# 这种下载的方式解压后直接可以使用，无需再编译安装  
```

配置`/etc/profile`添加rabbit-server环境变量,增加一行保存`export PATH=/usr/local/rabbitmq/sbin:$PATH`

```bash
# 修改环境变量添加 export PATH=/usr/local/rabbitmq/sbin:$PATH
[root@ip-172-31-17-64 local]# vi /etc/profile
```
```bash
# 刷新系统变量使之生效
[root@ip-172-31-17-64 local]# source /etc/profile
# 随后启用MQ管理方式，启动后台管理
[root@ip-172-31-17-64 local]# cd cd /usr/local/rabbitmq/sbin
[root@ip-172-31-17-64 sbin]# ./rabbitmq-plugins enable rabbitmq_management
# 启动rabbitmq
[root@ip-172-31-17-64 sbin]# ./rabbitmq-server -detached
Warning: PID file not written; -detached was passed.
# 关闭
[root@ip-172-31-17-64 sbin]# ./rabbitmqctl stop
# 添加用户
[root@ip-172-31-17-64 sbin]# ./rabbitmqctl add_user admin admin
# 授权
[root@ip-172-31-17-64 sbin]# ./rabbitmqctl set_permissions -p "/" admin ".*" ".*" ".*"
# 修改用户角色
[root@ip-172-31-17-64 sbin]# ./rabbitmqctl set_user_tags admin administrator
```

![e](https://volc1612.gitee.io/blog/images/rabbitmq/rabbitmq_a.png)

参考： https://blog.csdn.net/qq_35098526/article/details/84335030

### 1 windows下安装RabbitMQ
<!-- more -->
#### 1.1 注意事项
1.推荐使用3.4.1版本。
2.推荐使用默认的安装路径(c盘)
3.系统用户名必须是英文
```
C:\Users\Administrator>
```
4.计算机名必须是英文
5.系统的用户必须是管理员
6.
安装失败的同学应该如何解决：
	1、	重装系统  --  不推荐
	2、	将RabbitMQ安装到linux虚拟机中
		a)	推荐
	3、	使用别人安装好的RabbitMQ服务
		a)	只要给你开通一个账户即可。
		b)	使用公用的RabbitMQ服务。
		c)	推荐
#### 1.2 安装
下载地址：http://www.rabbitmq.com/download.html
##### 1.2.1 安装Erlang
下载地址：http://www.erlang.org/download/otp_win64_17.3.exe
以管理员身份运行安装，安装默认路径，安装过程中可能出现以下提示
![e](https://volc1612.gitee.io/blog/images/rabbitmq/e.png)
直接忽略即可。
##### 1.2.2 安装rabbitmq
以管理员身份安装，安装成功后，系统服务中有RabbitMQ服务，尝试停止、启动、重启确认安装成功。
##### 1.2.3 连接rabbitmq服务
以管理员身份进入安装目录(C:\Program Files (x86)\RabbitMQ Server\rabbitmq_server-3.4.1\sbin)目录下
执行以下命令
```
rabbitmq-plugins enable rabbitmq_management
```
##### 1.2.4 登录
浏览器访问:127.0.0.1:15672
默认账号：guest 密码：guest
[需要创建普通用户](#jump)


### 2 spring整合rabbitmq
#### 2.1 后台系统
##### 2.1.1 引入依赖
service项目中引入依赖
```
<!-- 在使用的最底层倒入依赖 spring封装好对rabbitmq消息队列的操作 -->
<dependency>
	<groupId>org.springframework.amqp</groupId>
	<artifactId>spring-rabbit</artifactId>
	<version>1.4.0.RELEASE</version>
</dependency>
```
##### 2.1.2 rabbitmq.properties属性文件
```
rabbitmq.host=127.0.0.1
rabbitmq.port=5672
rabbitmq.username=taotao
rabbitmq.password=taotao
rabbitmq.vhost=/taotao
```
##### 2.1.3 spring初始化配置文件applicationContext.xml加载rabbitmq.properties属性文件
```xml
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p" xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd">

	<!-- 使用spring自带的占位符替换功能 -->
	<bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
		<!-- 允许JVM参数覆盖 -->
		<property name="systemPropertiesModeName" value="SYSTEM_PROPERTIES_MODE_OVERRIDE" />
		<!-- 忽略没有找到的资源文件 -->
		<property name="ignoreResourceNotFound" value="true" />
		<!-- 配置资源文件 -->
		<property name="locations">
			<list>
				<value>classpath:jdbc.properties</value>
				<value>classpath:evn.properties</value>
				<value>classpath:redis.properties</value>
				<value>classpath:httpclient.properties</value>
				<value>classpath:rabbitmq.properties</value>
			</list>
		</property>
	</bean>

	<!-- 扫描包 -->
	<context:component-scan base-package="com.taotao" />

	<!-- 未实现读写分离时配置定义数据源 -->
	<!-- <bean id="dataSource" class="com.jolbox.bonecp.BoneCPDataSource" destroy-method="close"> 数据库驱动 <property name="driverClass" value="${jdbc.driverClassName}" /> 相应驱动的jdbcUrl <property name="jdbcUrl" 
		value="${jdbc.url}" /> 数据库的用户名 <property name="username" value="${jdbc.username}" /> 数据库的密码 <property name="password" value="${jdbc.password}" /> 检查数据库连接池中空闲连接的间隔时间，单位是分，默认值：240，如果要取消则设置为0 <property name="idleConnectionTestPeriod" 
		value="60" /> 连接池中未使用的链接最大存活时间，单位是分，默认值：60，如果要永远存活设置为0 <property name="idleMaxAge" value="30" /> 每个分区最大的连接数 判断依据：请求并发数 <property name="maxConnectionsPerPartition" value="100" /> 每个分区最小的连接数 <property name="minConnectionsPerPartition" 
		value="5" /> </bean> -->

	<!-- 配置连接池 -->
	<bean id="masterDataSource" class="com.jolbox.bonecp.BoneCPDataSource" destroy-method="close">
		<!-- 数据库驱动 -->
		<property name="driverClass" value="${jdbc.master.driver}" />
		<!-- 相应驱动的jdbcUrl -->
		<property name="jdbcUrl" value="${jdbc.master.url}" />
		<!-- 数据库的用户名 -->
		<property name="username" value="${jdbc.master.username}" />
		<!-- 数据库的密码 -->
		<property name="password" value="${jdbc.master.password}" />
		<!-- 检查数据库连接池中空闲连接的间隔时间，单位是分，默认值：240，如果要取消则设置为0 -->
		<property name="idleConnectionTestPeriod" value="60" />
		<!-- 连接池中未使用的链接最大存活时间，单位是分，默认值：60，如果要永远存活设置为0 -->
		<property name="idleMaxAge" value="30" />
		<!-- 每个分区最大的连接数 -->
		<property name="maxConnectionsPerPartition" value="150" />
		<!-- 每个分区最小的连接数 -->
		<property name="minConnectionsPerPartition" value="5" />
	</bean>

	<!-- 配置连接池 -->
	<bean id="slave01DataSource" class="com.jolbox.bonecp.BoneCPDataSource" destroy-method="close">
		<!-- 数据库驱动 -->
		<property name="driverClass" value="${jdbc.slave01.driver}" />
		<!-- 相应驱动的jdbcUrl -->
		<property name="jdbcUrl" value="${jdbc.slave01.url}" />
		<!-- 数据库的用户名 -->
		<property name="username" value="${jdbc.slave01.username}" />
		<!-- 数据库的密码 -->
		<property name="password" value="${jdbc.slave01.password}" />
		<!-- 检查数据库连接池中空闲连接的间隔时间，单位是分，默认值：240，如果要取消则设置为0 -->
		<property name="idleConnectionTestPeriod" value="60" />
		<!-- 连接池中未使用的链接最大存活时间，单位是分，默认值：60，如果要永远存活设置为0 -->
		<property name="idleMaxAge" value="30" />
		<!-- 每个分区最大的连接数 -->
		<property name="maxConnectionsPerPartition" value="150" />
		<!-- 每个分区最小的连接数 -->
		<property name="minConnectionsPerPartition" value="5" />
	</bean>

	<!-- 定义数据源，使用自己实现的数据源 -->
	<bean id="dataSource" class="com.taotao.manage.datasource.DynamicDataSource">
		<!-- 设置多个数据源 -->
		<property name="targetDataSources">
			<map key-type="java.lang.String">
				<!-- 这个key需要和程序中的key一致 -->
				<entry key="master" value-ref="masterDataSource" />
				<entry key="slave" value-ref="slave01DataSource" />
			</map>
		</property>
		<!-- 设置默认的数据源，这里默认走写库 -->
		<property name="defaultTargetDataSource" ref="masterDataSource" />
	</bean>

</beans>

```

##### 2.1.4 spring整合rabbitmq文件applicationContext-rabbitmq.xml
###### 2.1.4.1 加入spring-rabbit-1.4.xsd引用
为xml文件添加引用头文件
找到Add XML Catalog Element：
user specified entries 点击添加
Location:spring-rabbit-1.4.xsd文件路径
Key Type:Schema location
Key:http://www.springframework.org/schema/rabbit/spring-rabbit-1.4.xsd
导入xsd文件后，有关rabbitmq的配置就会有提示。
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:rabbit="http://www.springframework.org/schema/rabbit"
	xsi:schemaLocation="http://www.springframework.org/schema/rabbit
	http://www.springframework.org/schema/rabbit/spring-rabbit-1.4.xsd
	http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans-4.0.xsd">

	<!-- 定义RabbitMQ的连接工厂 -->
	<rabbit:connection-factory id="connectionFactory"
		host="${rabbitmq.host}" port="${rabbitmq.port}" username="${rabbitmq.username}"
		password="${rabbitmq.password}" virtual-host="${rabbitmq.vhost}" />

	<!-- 定义管理 用于声明交换机 -->
	<rabbit:admin connection-factory="connectionFactory" />

	<!-- 定义交换机持久化并自动声明的交换机,自动声明并不是在tomcat启动时声明，而是在使用中没有交换机时才声明，这时登录rabbitmq就会看到taotao-item_exchange交换机已创建 -->
	<rabbit:topic-exchange name="taotao-item_exchange"
		durable="true" auto-declare="true" />

	<!-- 定义模板 -->
	<rabbit:template id="template" connection-factory="connectionFactory"
		exchange="taotao-item_exchange" />
</beans>
```
###### 2.1.4.2 注入RabbitTemplate
spring整合rabbitmq文件配置成功后，在业务实现类里可直接注入RabbitTemplate。
通过交换机给其他系统发送消息，通配符模式示意图：
![t](https://volc1612.gitee.io/blog/images/rabbitmq/t.png)

业务处理类
```java

import java.util.HashMap;
import java.util.List;
import java.util.Map;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.github.abel533.entity.Example;
import com.github.pagehelper.PageHelper;
import com.github.pagehelper.PageInfo;
import com.taotao.common.service.ApiService;
import com.taotao.manage.mapper.ItemMapper;
import com.taotao.manage.pojo.Item;
import com.taotao.manage.pojo.ItemDesc;
import com.taotao.manage.pojo.ItemParamItem;

@Service
public class ItemService extends BaseService<Item> {

    // 在service中springmvc自动开启事物管理
    // 事物具有传播性，itemMDescService存在于ItemService中，所以说他们执行的方法在同一个事物中
    @Autowired
    private ItemDescService iteMDescService;

    @Autowired
    private ItemParamItemService itemParamItemService;

    @Autowired
    private ItemMapper itemMapper;

    @Autowired
    private ApiService apiService;

    @Value("${TAOTAO_WEB_UTL}")
    private String TAOTAO_WEB_UTL;

    @Autowired
    private RabbitTemplate rabbitTemplate;

    private static final ObjectMapper MAPPER = new ObjectMapper();

    public void saveItem(Item item, String desc, String itemParams) {
	// 设置初始值
	item.setStatus(1);
	item.setId(null); // 安全考虑，强制设置id为null
	super.save(item);
	ItemDesc ite = new ItemDesc();
	ite.setItemId(item.getId());
	ite.setItemDesc(desc);
	iteMDescService.save(ite);
	// 保存规格参数数据
	ItemParamItem itemParamItem = new ItemParamItem();
	itemParamItem.setItemId(item.getId());
	itemParamItem.setParamData(itemParams);
	this.itemParamItemService.save(itemParamItem);

	// 发送消息
	sendMsg(item.getId(), "insert");
    }

    /**
     * @Title queryPageList
     * @Description TODO
     * @param page
     * @param rows void
     * @throws 
     */
    public PageInfo<Item> queryPageList(Integer page, Integer rows) {
	Example example = new Example(Item.class);
	example.setOrderByClause("updated DESC");
	// 设置分页参数 一定要放在查询前面
	PageHelper.startPage(page, rows);
	List<Item> list = itemMapper.selectByExample(example);
	return new PageInfo<Item>(list);
    }

    /**
     * @Title updateItem
     * @Description 修改商品
     * @param item
     * @param desc void
     * @throws 
     */
    public void updateItem(Item item, String desc, String itemParams) {
	// 强制设置不能修改的字段为null
	item.setStatus(null);
	item.setCreated(null);
	super.updateSelective(item); // 选择不为null的字段作为修改
	// 修改商品描述数据
	ItemDesc itemDesc = new ItemDesc();
	itemDesc.setItemId(item.getId());
	itemDesc.setItemDesc(desc);
	this.iteMDescService.updateSelective(itemDesc);
	// 修改商品对应的规格参数
	this.itemParamItemService.udpateItemParamItem(item.getId(), itemParams);

	/*
	 * 通知前台系统其他系统商品已经更新了(调用前台对缓存的处理接口，这里前台是直接删除缓存的)
	 * 在service中不能trycatch，如果捕获异常了，就会影响事物的回滚
	 * 但是这里的逻辑是不属于正常逻辑的一部分(不需要事物回滚)，这里可以trycatch
	 */
	/*
	 * try { String url = TAOTAO_WEB_UTL + "/item/cache/" + item.getId() +
	 * ".html"; this.apiService.doPost(url, null); } catch (Exception e) {
	 * e.printStackTrace(); }
	 */
	/*
	 * 使用rabbitmq消息队列通知前台，代替上面的功能
	 */
	sendMsg(item.getId(), "update");
    }

    private void sendMsg(Long itemId, String type) {
	try {
	    Map<String, Object> msg = new HashMap<String, Object>();
	    msg.put("itemId", itemId);
	    msg.put("type", type);
	    msg.put("date", System.currentTimeMillis()); // 当前时间戳
	    // 参数：1.使用通配符方式通知 2.数据以jso格式传输
	    this.rabbitTemplate.convertAndSend("item." + type, MAPPER.writeValueAsString(msg));
	} catch (Exception e) {
	    e.printStackTrace();
	}
    }
}
```
#### 2.2 前台系统
1.引入jar包
2.rabbitmq.properties属性文件
3.初始化rabbitmq.properties属性文件applicationContext-rabbitmq.xml
4.spring整合rabbitmq文件
```xml
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:rabbit="http://www.springframework.org/schema/rabbit"
	xsi:schemaLocation="http://www.springframework.org/schema/rabbit
	http://www.springframework.org/schema/rabbit/spring-rabbit-1.4.xsd
	http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans-4.0.xsd">

	<!-- 定义RabbitMQ的连接工厂 -->
	<rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}" port="${rabbitmq.port}" username="${rabbitmq.username}" password="${rabbitmq.password}"
		virtual-host="${rabbitmq.vhost}" />

	<!-- 定义管理 用于声明交换机 -->
	<rabbit:admin connection-factory="connectionFactory" />

	<!-- 定义队列 ,一个持久化自动声明的队列 ，需要绑定后台系统(taotao-manage-web)创建的交换机，这种绑定要人工维护实现(设置权限更灵活) -->
	<rabbit:queue name="taotao-web-item-queue" durable="true" auto-declare="true" />

	<!-- 定义消费者 -->
	<bean id="itemMQHandler" class="com.taotao.web.mq.handler.ItemMQHandler" />

	<!-- 创建监听 ,消费者监听队列 -->
	<rabbit:listener-container connection-factory="connectionFactory">
		<rabbit:listener ref="itemMQHandler" method="excute" queue-names="taotao-web-item-queue" />
	</rabbit:listener-container>
</beans>
```
5.消费者监听队列处理类
ItemMQHandler
```java

import org.springframework.beans.factory.annotation.Autowired;
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.taotao.common.service.RedisService;
import com.taotao.web.service.ItemService;

/**
 * @ClassName ItemMQHandler
 * @Author volc
 * @Description rabbitmq监听， 用于处理监听到的消息
 * @Date 2017年3月2日 下午2:34:37
 */
public class ItemMQHandler {

    private static final ObjectMapper MAPPER = new ObjectMapper();

    @Autowired
    private RedisService redisService;

    /**
     * @MethodName excute
     * @Author volc
     * @Description 处理后台发送的消息，这里捕获到rabbitmq进行处理
     * @Date 2017年3月2日 下午2:50:31
     */
    public void excute(String message) {
	try {
	    JsonNode jsonNode = MAPPER.readTree(message);
	    Long itemId = jsonNode.get("itemId").asLong();
	    String key = ItemService.REDIS_KEY + itemId;
	    this.redisService.del(key);
	} catch (Exception e) {
	    e.printStackTrace();
	}
    }

}
```
6.[队列和交换机绑定](#jump)

### 2 Linux下安装RabbitMQ
#### 1、安装Erlang
```
# 方式一
cd /usr/local/src/
mkdir rabbitmq
cd rabbitmq

wget http://packages.erlang-solutions.com/erlang-solutions-1.0-1.noarch.rpm
rpm -Uvh erlang-solutions-1.0-1.noarch.rpm

rpm --import http://packages.erlang-solutions.com/rpm/erlang_solutions.asc

sudo yum install erlang
# 方式二(推荐使用)
上传：esl-erlang_17.3-1~centos~6_amd64.rpm
执行 yum install esl-erlang_17.3-1~centos~6_amd64.rpm
上传：esl-erlang-compat-R14B-1.el6.noarch.rpm
yum install esl-erlang-compat-R14B-1.el6.noarch.rpm
```
#### 2、安装RabbitMQ

```bash
mkdir /user/local/src/rabbitmq/
cd /user/local/src/rabbitmq/
# 上传rabbitmq-server-3.4.1-1.noarch.rpm安装文件
rz
# 安装命令
rpm -ivh rabbitmq-server-3.4.1-1.noarch.rpm
# rabbitmq服务命令
cd /user/local/src/rabbitmq/
service rabbitmq-server start
service rabbitmq-server stop
service rabbitmq-server restart
# 发现启动很慢，原因是rabbitmq启动时需要获取到主机名称，命令行前面的含义[root@主机名称 rebbitmq]
# rabbitmq，会执行ping 主机名称 结果ping不到，linux需要配置类似window中的host文件
vim /etc/hosts
# 添加一条信息，主机名称为命令行前面[root@主机名称 rebbitmq]中的主机名称
127.0.0.1 主机名称
# 保存退出
# 重新启动rabbitmq服务
service rabbitmq-server start
# 启动成功
```

```bash
# 创建用户
[root@izbp117v57iiomcgxcb42mz ~]# sudo rabbitmqctl add_user name passworld
# 设置权限
[root@izbp117v57iiomcgxcb42mz ~]# sudo rabbitmqctl set_user_tags name administrator
# 授权用户
[root@izbp117v57iiomcgxcb42mz ~]# sudo rabbitmqctl  set_permissions -p "/" name '.*' '.*' '.*'  
# 修改密码
[root@izbp117v57iiomcgxcb42mz ~]# sudo rabbitmqctl change_password name newPwd
```

#### 3、配置
```
cd /etc/rabbitmq
# 复制配置文件
cp /usr/share/doc/rabbitmq-server-3.4.1/rabbitmq.config.example /etc/rabbitmq/
# mv重命名
mv rabbitmq.config.example rabbitmq.config
# 开启普通用户远程访问
vi /etc/rabbitmq/rabbitmq.config
# 搜索{loopback_users, []}，去掉前面的%%和后面的, 保存退出
# 重启rabbitmq服务
service rabbitmq-server restart
# 设置开机启动
chkconfig rabbitmq-server on
```
#### 4、开启web界面管理工具
```
cd /etc/rabbitmq
rabbitmq-plugins enable rabbitmq_management
#开放5672和15672两个端口
/sbin/iptables -I INPUT -p tcp --dport 15672 -j ACCEPT
/sbin/iptables -I INPUT -p tcp --dport 5672 -j ACCEPT
/etc/rc.d/init.d/iptables save
```
#### 5、创建用户
浏览器访问，服务器ip+rabbitmq访问端口
192.168.10.25:15672
默认账号：guest 密码：guest
创建用户

### 3 <span id="jump">创建用户绑定交换机</span>
本地登录地址：http://127.0.0.1:15672
<!-- ![rabbitmq](https://volc1612.gitee.io/blog/images/rabbitmq/login.PNG) -->
![rabbitmq](https://volc1612.gitee.io/blog/images/rabbitmq/login.PNG)
1、创建普通用户
rabbitmq默认账户为：guest 密码：guest，拥有最高权限，这里需要创建普通权限的用户
![rabbitmq](https://volc1612.gitee.io/blog/images/rabbitmq/create.gif)
2、为用户添加Virtual Hosts访问权限
![rabbitmq](https://volc1612.gitee.io/blog/images/rabbitmq/createv.gif)
3、监听队列绑定交换机，实现数据同步。
1) 后台系统声明交换机(定义交换机持久化并自动声明的交换机,自动声明并不是在tomcat启动时声明，而是在使用中没有交换机时才声明)。
2) 其他系统定义队列(一个持久化自动声明的队列 ，需要绑定后台系统(taotao-manage-web)创建的交换机，这种绑定要人工维护实现(设置权限更灵活))。
3) 当后台数据改变时，会发送一个消息给队列，其他系统监听队列是否有消息存在，如果有消息在本系统进行处理。

使用projectname账户登录rabbitmq后台，进行绑定：
1.选择后台创的交换机taotao-item_exchange
2.在Bindings添加前台创建的队列taotao-web-item-queue
3.指定通配符，监听处理选择
	搜索系统需要获取数据的更新，删除和添加消息
	taotao-web前台系统需要获取数据的更新和删除消息
整体流程：
	当后台(生产者)发送消息给交换机(该交换机是后台创建)，该交换机通配符将消息传递给绑定该交换机的指定的队列(该队列是前台系统创建)，前台的监听会捕获自己队列中的消息交给com.taotao.web.mq.handler.ItemMQHandler进行处理(消费者)。
![rabbitmq](https://volc1612.gitee.io/blog/images/rabbitmq/binding.gif)