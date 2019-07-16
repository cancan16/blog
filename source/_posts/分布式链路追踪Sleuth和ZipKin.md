---
title: 分布式链路追踪Sleuth和ZipKin
date: 2019-06-30 17:06:30
update: 2019-07-16 17:06:30
categories: SPRINGCLOUD
tags: [链路追踪]
---

### Sleuth组件

官方文档
http://cloud.spring.io/spring-cloud-static/Finchley.SR1/single/spring-cloud.html#sleuth-adding-project

#### 什么是Sleuth

一个组件，专门用于记录链路数据的开源组件，接口日志埋点。

#### 使用

添加依赖
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
```

浏览器访问order-service服务接口，控制台打印出日志

```
INFO [order-service,d57cb07dc038d4d5,d01d2e4c91ecfa97,false]
```

[order-service,96f95a0dd81fe3ab,852ef4cfcdecabf3,false]

1、第一个值，spring.application.name的值

2、第二个值，96f95a0dd81fe3ab ，sleuth生成的一个ID，叫Trace ID，用来标识一条请求链路，一条请求链路中包含一个Trace ID，多个Span ID

3、第三个值，852ef4cfcdecabf3、spanid 基本的工作单元，获取元数据，如发送一个http

4、第四个值：false，是否要将该信息输出到zipkin服务中来收集和展示。

### 分布式链路追踪组件Sleuth常见问题说明

IDEA控制台只打印一次链路追踪组件的INFO日志

解决：接口层添加info级别日志

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * 商品接口
 */
@RestController
@RequestMapping
public class ProductController {

    private final Logger logger = LoggerFactory.getLogger(getClass());

    @Value("${server.port}")
    private Integer port;

    @GetMapping("/findProductInfo")
    public String findProductInfo() {
        logger.info("123");
        return "订单服务获取商品信息， 端口号： " + port;
    }
}
```

### 可视化链路追踪系统ZipKin部署

#### 什么是zipkin

<a href="https://zipkin.io/">官网</a>

大规模分布式系统的APM工具（Application Performance Management）,基于Google Dapper的基础实现，和sleuth结合可以提供可视化web界面分析调用链路耗时情况。

#### 同类产品

* 鹰眼（EagleEye）
* CAT
* twitter开源zipkin，结合sleuth
* Pinpoint，运用JavaAgent字节码增强技术
* StackDriver Trace (Google) 

#### 开始使用

https://github.com/openzipkin/zipkin
https://zipkin.io/pages/quickstart.html

zipkin组成：Collector、Storage、Restful API、Web UI组成

#### 知识拓展：OpenTracing

OpenTracing 已进入 CNCF，正在为全球的分布式追踪，提供统一的概念和数据标准。 
通过提供平台无关、厂商无关的 API，使得开发人员能够方便的添加（或更换）追踪系统的实现。


#### 推荐阅读

http://blog.daocloud.io/cncf-3/
https://www.zhihu.com/question/27994350
https://yq.aliyun.com/articles/514488?utm_content=m_43347


### 高级篇幅之链路追踪组件Zipkin+Sleuth实战

简介：使用Zipkin+Sleuth业务分析调用链路分析实战

#### 文档

http://cloud.spring.io/spring-cloud-static/Finchley.SR1/single/spring-cloud.html#_sleuth_with_zipkin_via_http

sleuth收集跟踪信息通过http请求发送给zipkin server，zipkinserver进行跟踪信息的存储以及提供Rest API即可，Zipkin UI调用其API接口进行数据展示

默认存储是内存，可也用`mysql`、或者`elasticsearch`等存储

#### 加入依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

里面包含`spring-cloud-starter-sleuth`、`spring-cloud-sleuth-zipkin`

#### 文档说明

http://cloud.spring.io/spring-cloud-static/Finchley.SR1/single/spring-cloud.html#_features_2

#### 配置zipkin.base-url

#### 配置采样百分闭spring.sleuth.sampler


#### 推荐资料

https://blog.csdn.net/jrn1012/article/details/77837710