---
title: 分布式链路追踪Sleuth和ZipKin
date: 2019-06-30 17:06:30
update: 2019-06-30 17:06:30
categories: SPRINGCLOUD
tags: [链路追踪]
---

### Sleuth组件

官方文档
http://cloud.spring.io/spring-cloud-static/Finchley.SR1/single/spring-cloud.html#sleuth-adding-project

#### 什么是Sleuth

一个组件，专门用于记录链路数据的开源组件

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