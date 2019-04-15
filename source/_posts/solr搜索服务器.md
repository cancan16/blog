---
title: solr搜索服务器
date: 2017-03-03 00:05:13
update: 2017-03-05 00:05:13
categories: JAVA
tags: [java, solr, linux]
---
### 1 windows下使用solr
<!-- more -->
#### 1.1 解压solr-4.10.2.zip文件
cmd进入D:\solr-4.10.2\example目录
执行启动命令
```
D:\solr-4.10.2\example>java -jar start.jar
```
启动成功，浏览器访问
访问url: http://127.0.0.1:8983
修改D:\solr-4.10.2\example\contexts\solr-jetty-context.xml文件
```
<Set name="contextPath"><SystemProperty name="hostContext" default="/"/></Set>
```
通过nginx代理通过域名客直接访问。

#### 1.2 创建指定的项目solr core
1、	在example目录下创建taotao-solr文件夹；
2、	将./solr下的solr.xml拷贝到taotao-solr目录下；
3、	在taotao-solr下创建taotao目录，并且在taotao目录下创建conf和data目录；
4、	将example\solr\collection1\core.properties文件拷贝到example\taotao-solr\taotao下，并且修改name=taotao；
5、	将example\solr\collection1\conf下的schema.xml、solrconfig.xml拷贝到example\taotao-solr\taotao\conf下；
6、	修改schema.xml文件，使其配置最小化：
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<schema name="example" version="1.5">
	<field name="_version_" type="long" indexed="true" stored="true"/>
	<field name="_root_" type="string" indexed="true" stored="false"/>
	<!--
		配置tb_item表中的字段是否加入到solr中
		字段名称(小驼峰) 类型 是否加索引 是否存储(查询的时候需要这个字段就要存储) 是否必须 是否有多个值
	-->
	<field name="id" type="long" indexed="true" stored="true" required="true" multiValued="false" />
	<field name="title" type="text_ik" indexed="true" stored="true" />
	<field name="sellPoint" type="string" indexed="false" stored="true" />
	<field name="price" type="long" indexed="true" stored="true" />
	<field name="image" type="string" indexed="false" stored="true" />
	<field name="cid" type="long" indexed="true" stored="true" />	
	<field name="status" type="int" indexed="true" />
	<field name="updated" type="long" indexed="true" stored="true" />
	<uniqueKey>id</uniqueKey>
    <fieldType name="string" class="solr.StrField" sortMissingLast="true" />
    <fieldType name="int" class="solr.TrieIntField" precisionStep="0" positionIncrementGap="0"/>
    <fieldType name="long" class="solr.TrieLongField" precisionStep="0" positionIncrementGap="0"/>
	<!-- 中文分词器 -->
	<fieldType name="text_ik" class="solr.TextField">   
		<analyzer class="org.wltea.analyzer.lucene.IKAnalyzer"/>   
	</fieldType>
	<solrQueryParser defaultOperator="AND"/>
</schema>
```
7、修改solrconfig.xml文件，修改一些配置，大部分配置先保持默认：
a)	将所有的<lib>标签注释掉；
b)	搜索<str name="df">text</str>替换成<str name="df">title</str>
c)	将<searchComponent name="elevator" class="solr.QueryElevationComponent" ></searchComponent>这部分注释掉（这个的功能类似百度的竞价排名）
8、启动solr：
```
java -Dsolr.solr.home=taotao-solr -jar start.jar
```

#### 1.3 添加IK中文分词器的支持
1、	将IKAnalyzer-2012-4x.jar拷贝到example\solr-webapp\webapp\WEB-INF\lib下；
2、	在schema.xml文件中添加fieldType：
<fieldType name="text_ik" class="solr.TextField">   
     <analyzer class="org.wltea.analyzer.lucene.IKAnalyzer"/>   
</fieldType>

### 2 导入项目中需要加入到solr的数据
需要使用solr搜索服务的数据应先存放到solr中。
解析数据参考：<a href="http://volc1612.gitee.io/blog/2017/05/16/fastJson%E8%A7%A3%E6%9E%90json%E5%AD%97%E7%AC%A6%E4%B8%B2/">jackson解析字符串</a>

```java

import java.util.List;
import org.apache.commons.lang3.StringUtils;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;
import org.apache.solr.client.solrj.impl.HttpSolrServer;
import org.apache.solr.client.solrj.impl.XMLResponseParser;
import org.junit.Before;
import org.junit.Test;
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import cn.itcast.solrj.pojo.Item;

public class ItemDataImportTest {

    private HttpSolrServer httpSolrServer;

    private static final ObjectMapper MAPPER = new ObjectMapper();

    @Before
    public void setUp() throws Exception {
	// 在url中指定core名称：taotao
	// http://solr.taotao.com/#/taotao -- 界面操作
	String url = "http://solr.taotao.com/taotao"; // 服务地址
	HttpSolrServer httpSolrServer = new HttpSolrServer(url); // 定义solr的server
	httpSolrServer.setParser(new XMLResponseParser()); // 设置响应解析器
	httpSolrServer.setMaxRetries(1); // 设置重试次数，推荐设置为1
	httpSolrServer.setConnectionTimeout(500); // 建立连接的最长时间

	this.httpSolrServer = httpSolrServer;
    }

    @Test
    public void testData() throws Exception {
	// 通过后台系统的接口查询商品数据
	String url = "http://manage.taotao.com/rest/item?page={page}&rows=100";
	int page = 1;
	int pageSzie = 0;
	do {
	    String u = StringUtils.replace(url, "{page}", "" + page);
	    System.out.println(u);
	    String jsonData = doGet(u);
	    JsonNode jsonNode = MAPPER.readTree(jsonData);
	    String rowsStr = jsonNode.get("rows").toString();
	    List<Item> items = MAPPER.readValue(rowsStr, MAPPER.getTypeFactory().constructCollectionType(List.class, Item.class));
	    pageSzie = items.size();
	    this.httpSolrServer.addBeans(items);
	    this.httpSolrServer.commit();
	    page++;
	} while (pageSzie == 100);

    }

    private String doGet(String url) throws Exception {
	// 创建Httpclient对象
	CloseableHttpClient httpclient = HttpClients.createDefault();

	// 创建http GET请求
	HttpGet httpGet = new HttpGet(url);

	CloseableHttpResponse response = null;
	try {
	    // 执行请求
	    response = httpclient.execute(httpGet);
	    // 判断返回状态是否为200
	    if (response.getStatusLine().getStatusCode() == 200) {
		return EntityUtils.toString(response.getEntity(), "UTF-8");
	    }
	} finally {
	    if (response != null) {
		response.close();
	    }
	    httpclient.close();
	}
	return null;
    }

    @Test
    public void testData2() throws Exception {

	//
	Item item = new Item();
	item.setCid(1L);
	item.setId(111L);
	item.setImage("http://image.taotao.com/jd/4ef8861cf6854de9889f3db9b24dc371.jpg");
	item.setPrice(299000L);
	item.setSellPoint("清仓！仅北京，武汉仓有货！");
	item.setStatus(1);
	item.setTitle("阿尔卡特 (OT-927) 炭黑 联通3G手机 双卡双待");
	item.setUpdated(System.currentTimeMillis());

	this.httpSolrServer.addBean(item);
	this.httpSolrServer.commit();

    }

}

```

### 3 spring整合solr搜索
#### 3.1 引入solr客户端jar包solrj
```
<!-- solr客户端 -->
<dependency>
	<groupId>org.apache.solr</groupId>
	<artifactId>solr-solrj</artifactId>
	<version>4.10.2</version>
</dependency>
```
#### 3.2 solr.properties属性配置文件
```
solr.url=http://solr.taotao.com/taotao
solr.maxRetries=1
solr.connectionTimeout=500
```
#### 3.3 taotao-search-servlet.xml文件中初始化solr.properties文件
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd">

	<!-- 使用spring自带的占位符替换功能 -->
	<bean
		class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
		<!-- 允许JVM参数覆盖 -->
		<property name="systemPropertiesModeName" value="SYSTEM_PROPERTIES_MODE_OVERRIDE" />
		<!-- 忽略没有找到的资源文件 -->
		<property name="ignoreResourceNotFound" value="true" />
		<!-- 配置资源文件 -->
		<property name="locations">
			<list>
				<value>classpath:env.properties</value>
				<value>classpath:httpclient.properties</value>
				<value>classpath:solr.properties</value>
				<value>classpath:rabbitmq.properties</value>
			</list>
		</property>
	</bean>
	<!-- 扫描包 -->
	<context:component-scan base-package="com.taotao"/>

</beans>

```
#### 3.4 applicationContext-solr.xml文件spring整合solr文件
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd">

	<!-- 创建solr对象 -->
	<bean class="org.apache.solr.client.solrj.impl.HttpSolrServer">
		<constructor-arg index="0" value="${solr.url}">
		</constructor-arg>
		<!-- 设置响应解析器 -->
		<property name="parser">
			<bean class="org.apache.solr.client.solrj.impl.XMLResponseParser"></bean>
		</property>
		<!-- 设置重试次数 -->
		<property name="maxRetries" value="${solr.maxRetries}" />
		<!-- 连接的超时时间 -->
		<property name="connectionTimeout" value="${solr.connectionTimeout}" />
	</bean>
</beans>

```

#### 3.5 配置nginx代理

```xml
server {
        listen       80;
        server_name  search.volc.cn;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

		proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Server $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header Host $host;
        location / {
			proxy_pass http://127.0.0.1:8085;
			proxy_connect_timeout 600;
			proxy_read_timeout 600;
        }
        
    }
```
#### 3.6 项目中搜索商品数据的接口(含分页)
<a href="https://github.com/volc1605/taotao/blob/master/src/taotao-search/trunck/src/main/java/com/taotao/search/controller/SearchController.java">
	接口访问solr服务接口示例
</a>

#### 3.7 解决乱码问题
get请求使用spring配置过滤是解决不了的。
接口中对数据重新编码:
```java
// 解决GET前段传入参数的编码格式为utf-8
keyWords = new String(keyWords.getBytes("ISO-8859-1"), "UTF-8");
```

### 4 Linux下部署搜索项目
<!-- more -->
修改
nginx solr.taotao.com
启动solr
java -jar start.jar
浏览器访问
solr.taotao.com/solr

修改D:\solr-4.10.2\example\contexts\solr-jetty-context.xml文件
```
<Set name="contextPath"><SystemProperty name="hostContext" default="/"/></Set>
```
目的是可以solr.taotao.com这样访问

放置独立的搜索项目
启动方式
```
cd D:\solr-4.10.2\example
java -Dsolr.solr.home=taotao-solr -s jar start.jar
```
因为linux系统中有java运行环境，只需要复制本地solr中的D:\solr-4.10.2\example文件夹下的文件和文件夹

```
切换到ucenter用户；
mkdir /ucenter/web/
# 上传taotao-solr-4.10.1.tar.gz
rz

tar –xvf taotao-solr-4.10.1.tar.gz
# 修改/ucenter/web/example目录为/ucenter/web/taotao-solr
mv example taotao-solr

cd taotao-solr/
# 启动solr,在linux系统中使用&符号，代表服务在后台运行，ctrl + c后查看进程，solr仍然是启动状态
# 类似redis中redis.conf里的daemonize yes，意思是作为后台启动配置
java -Dsolr.solr.home=taotao-solr -jar start.jar &
# 输出结果有8861，这个就是启动solr的进程号
# 开放8983solr默认端口号，这里和本地一样是通过nginx代理访问的
# nginx.conf配置solr访问端口
server {
	listen       80;
	server_name  solr.taotao.com;

	#charset koi8-r;

	#access_log  logs/host.access.log  main;

	proxy_set_header X-Forwarded-Host $host;
	proxy_set_header X-Forwarded-Server $host;
	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	proxy_set_header Host $host;
	location / {
		proxy_pass http://安装solr所在的服务器ip:8983;
		proxy_connect_timeout 600;
		proxy_read_timeout 600;
	}
}
/sbin/iptables -I INPUT -p tcp --dport 8983 -j ACCEPT
/etc/rc.d/init.d/iptables save
# 查看solr进程
ps -ef | grep java
```