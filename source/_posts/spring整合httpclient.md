---
title: spring整合httpclient
date: 2017-06-02 00:47:49
update: 2017-06-02 00:47:49
categories: JAVA
tags: [httpclient]
---

### 1 spring整合httpclient
<!-- more -->
#### 1.1 导入依赖

```xml
<!-- httpclient -->
<dependency>
	<groupId>org.apache.httpcomponents</groupId>
	<artifactId>httpclient</artifactId>
</dependency>
```
#### 1.2 httpclient配置文件
##### 1.2.1 httpclient.properties属性值配置文件
```xml
http.maxTotal=200
http.defaultMaxPerRoute=50
http.connectTimeout=1000
http.connectionRequestTimeout=500
http.socketTimeout=1000
http.staleConnectionCheckEnabled=true
```
##### 1.2.2 applicationContext.xml配置加载配置文件，spring配置文件中就可以使用${}来获取httpclient.properties属性值
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
				<value>classpath:redis.properties</value>
				<value>classpath:rabbitmq.properties</value>
			</list>
		</property>
	</bean>
	<!-- 扫描包 -->
	<context:component-scan base-package="com.taotao"/>

</beans>

```

##### 1.2.3 applicationContext-httpclient.xml
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd">

	<!-- 定义http链接管理器 -->
	<bean id="httpClientConnectionManager"
		class="org.apache.http.impl.conn.PoolingHttpClientConnectionManager">
		<!-- 值写到配置文件中 -->
		<property name="maxTotal" value="${http.maxTotal}" /><!-- 最大连接数,主机链接量 -->
		<property name="defaultMaxPerRoute" value="${http.defaultMaxPerRoute}" />
		<!-- 每个主机的最大并发数 -->
	</bean>
	<!-- 定义httpclient构建器 -->
	<bean id="httpClientBuilder" class="org.apache.http.impl.client.HttpClientBuilder">
		<property name="connectionManager" ref="httpClientConnectionManager" />
	</bean>
	<!-- 定义httpclient对象 -->
	<!-- 指定通过httpClientBuilder对象的build方法获取到httpclient对象, scope允许多例创建不同的httpclient对象 -->
	<!-- 该对象是多例的 -->
	<bean id="closeableHttpClient" class="org.apache.http.impl.client.CloseableHttpClient"
		factory-bean="httpClientBuilder" factory-method="build" scope="prototype" />
	<!-- 请求参数的构建器 -->
	<bean id="requestConfigBuilder" class="org.apache.http.client.config.RequestConfig.Builder">
		<!-- 建立连接的最长时间 -->
		<property name="connectTimeout" value="${http.connectTimeout}"></property>
		<!-- 从连接池获取到连接的最长时间 -->
		<property name="connectionRequestTimeout" value="${http.connectionRequestTimeout}"></property>
		<!-- 数据传输的最长时间 -->
		<property name="socketTimeout" value="${http.socketTimeout}"></property>
		<!-- 请求连接时测试连接是否可用 -->
		<property name="staleConnectionCheckEnabled" value="${http.staleConnectionCheckEnabled}"></property>
	</bean>
	<!-- 定义请求参数对象，是单利对象 -->
	<bean id="requestConfig" class="org.apache.http.client.config.RequestConfig"
		factory-bean="requestConfigBuilder" factory-method="build" />
	<!-- 定期关闭无效的连接 -->
	<bean id="idleConnectionEvictor" class="com.taotao.common.httpclient.IdleConnectionEvictor">
		<constructor-arg index="0" ref="httpClientConnectionManager" />
	</bean>
</beans>
```
##### 1.2.4 定时关闭httpclient对象无效链接
IdleConnectionEvictor.java
```java

import org.apache.http.conn.HttpClientConnectionManager;

/**
 * @ClassName IdleConnectionEvictor
 * @Author volc
 * @Description 定期关闭绑定线程的httpclient对象
 * @Date 2017年2月27日 下午8:20:22
 */
public class IdleConnectionEvictor extends Thread {

    private final HttpClientConnectionManager connMgr;

    private volatile boolean shutdown;

    public IdleConnectionEvictor(HttpClientConnectionManager connMgr) {
	this.connMgr = connMgr;
		// 加载applicationContext-httpclient.xml，启动当前线程
		this.start();
    }

    @Override
    public void run() {
	try {
	    while (!shutdown) {
		synchronized (this) {
		    wait(5000);
		    // 关闭失效的连接
		    connMgr.closeExpiredConnections();
		}
	    }
	} catch (InterruptedException ex) {
	    // 结束
	}
    }

    public void shutdown() {
	shutdown = true;
	synchronized (this) {
	    notifyAll();
	}
    }
}

```
##### 1.2.4 请求bean
```java
package com.taotao.common.httpclient;

/**
 * @ClassName HttpResult
 * @Author volc
 * @Description http请求结果
 * @Date 2017年2月25日 上午10:29:07
 */
public class HttpResult {

    private Integer code; // 状态码
    private String data; // 返回数据

    @Override
    public String toString() {
	return "HttpResult [code=" + code + ", data=" + data + "]";
    }

    public HttpResult(Integer code, String data) {
	this.code = code;
	this.data = data;
    }

    public HttpResult() {
    }

    public Integer getCode() {
	return code;
    }

    public void setCode(Integer code) {
	this.code = code;
    }

    public String getData() {
	return data;
    }

    public void setData(String data) {
	this.data = data;
    }
}


```
##### 1.2.5 封装请求通用方法ApiService
```java

import java.io.IOException;
import java.net.URISyntaxException;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import org.apache.http.NameValuePair;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.config.RequestConfig;
import org.apache.http.client.entity.UrlEncodedFormEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.message.BasicNameValuePair;
import org.apache.http.util.EntityUtils;
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.BeanFactoryAware;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import com.taotao.common.httpclient.HttpResult;

/**
 * @ClassName ApiService
 * @Author volc
 * @Description 封装通用的请求方式
 * @Date 2017年2月25日 上午12:53:33
 */
@Service
public class ApiService implements BeanFactoryAware {
    // 注入httpclient对象
    /**
     * httpclient应该是多例的，但是在这里已经注入了，调用service时就是单例，应该修改为多例模式
     */
    // @Autowired
    // private CloseableHttpClient httpclient;

    /**
     * @Fields beanFactory
     * bean工厂可以解决httpclient多例的问题
     */
    private BeanFactory beanFactory;
	// 需要设置RequestConfig不是必须注入的。
    @Autowired(required = false)
    private RequestConfig requestConfig;

    /**
     * @MethodName doGet 
     * @Author volc
     * @Description 不带参数的get请求， 执行GET请求，响应200返回内容，如果响应404返回null
     * @Date 2017年2月25日 上午12:54:48
     */
    public String doGet(String url) throws ClientProtocolException, IOException {
	// 创建http GET请求
	HttpGet httpGet = new HttpGet(url);
	httpGet.setConfig(requestConfig);
	CloseableHttpResponse response = null;
	try {
	    // 执行请求
	    response = getHttpClient().execute(httpGet);
	    // 判断返回状态是否为200
	    if (response.getStatusLine().getStatusCode() == 200) {
		return EntityUtils.toString(response.getEntity(), "UTF-8");
	    }
	} finally {
	    if (response != null) {
		response.close();
	    }
	}
	return null;
    }

    /**
     * @MethodName doGet 
     * @Author volc
     * @Description 带参数的GET请求
     * @Date 2017年2月25日 上午12:55:13
     */
    public String doGet(String url, Map<String, String> params) throws ClientProtocolException, IOException, URISyntaxException {
	// 创建构建uri对象
	URIBuilder uriBuilder = new URIBuilder(url);
	for (Map.Entry<String, String> entry : params.entrySet()) {
	    uriBuilder.setParameter(entry.getKey(), entry.getValue());
	}
	return doGet(uriBuilder.build().toString());
    }

    /**
     * @MethodName doPost 
     * @Author volc
     * @Description 执行post请求 封装通用的post请求(带参和不带参)
     * @Date 2017年2月25日 上午10:40:09
     */
    public HttpResult doPost(String url, Map<String, String> params) throws ClientProtocolException, IOException, URISyntaxException {
	// 创建http POST请求
	HttpPost httpPost = new HttpPost(url);
	httpPost.setConfig(requestConfig);
	if (null != params) {
	    // 设置1个post参数
	    List<NameValuePair> parameters = new ArrayList<NameValuePair>(0);
	    for (Map.Entry<String, String> entry : params.entrySet()) {
		parameters.add(new BasicNameValuePair(entry.getKey(), entry.getValue()));
	    }
	    // 构造一个form表单式的实体
	    UrlEncodedFormEntity formEntity = new UrlEncodedFormEntity(parameters, "UTF-8");
	    // 将请求实体设置到httpPost对象中
	    httpPost.setEntity(formEntity);
	}
	CloseableHttpResponse response = null;
	try {
	    // 执行请求
	    response = getHttpClient().execute(httpPost);
	    // 无论什么情况都返回
	    return new HttpResult(response.getStatusLine().getStatusCode(), EntityUtils.toString(response.getEntity(), "UTF-8"));
	} finally {
	    if (response != null) {
		response.close();
	    }
	}
    }

    /**
     * @MethodName getHttpClient 
     * @Author volc
     * @Description 封装获取多例的httpclient，因为spring注入的时httpclient为多例，每次获取都是多例的
     * @Date 2017年2月25日 上午10:52:21
     */
    private CloseableHttpClient getHttpClient() {
	return this.beanFactory.getBean(CloseableHttpClient.class);
    }

    /**
     * @Title setBeanFactory
     * @Description 设置spring注入的bean为多例,spring初始化时会调用这个覆盖的方法,(设置beanFactory对象)
     * @param beanFactory
     * @throws BeansException
     * @throws 
     */
    @Override
    public void setBeanFactory(BeanFactory beanFactory) throws BeansException {
	this.beanFactory = beanFactory;
    }
    
    /**
     * @MethodName doPostJson
     * @Author volc
     * @Description 请求类型：APPLICATION_JSON，方式：post
     * @Date 2017年3月1日 下午12:38:31
     */
    public HttpResult doPostJson(String url, String json) throws ClientProtocolException, IOException, URISyntaxException {
	// 创建http POST请求
	HttpPost httpPost = new HttpPost(url);
	httpPost.setConfig(requestConfig);
	if (null != json) {
	    // 构造一个form表单式的实体
	    StringEntity stringEntity = new StringEntity(json, ContentType.APPLICATION_JSON);
	    // 将请求实体设置到httpPost对象中
	    httpPost.setEntity(stringEntity);
	}
	CloseableHttpResponse response = null;
	try {
	    // 执行请求
	    response = getHttpClient().execute(httpPost);
	    // 无论什么情况都返回
	    return new HttpResult(response.getStatusLine().getStatusCode(), EntityUtils.toString(response.getEntity(), "UTF-8"));
	} finally {
	    if (response != null) {
		response.close();
	    }
	}
    }
}
```
以上3个类应该放到common项目中，以便其他项目使用http请求。
总结：单例对象(ApiService)中使用多例对象(CloseableHttpClient)通用接解决方法
	ApiService实现BeanFactoryAware接口
