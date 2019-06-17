---
title: 网关zuul
date: 2019-05-12 22:38:52
update: 2019-05-12 22:38:52
categories: SPRINGCLOUD
tags: [zuul]
---

### 微服务网关介绍和使用场景

`API Gateway`，是系统的唯一对外的入口，介于客户端和服务器端之间的中间层，处理非业务功能 提供路由请求、鉴权、监控、缓存、限流等功能。

<!-- more -->

> 统一接入

* 智能路由
* AB测试、灰度测试
* 负载均衡、容灾处理
* 日志埋点（类似Nignx日志）

> 流量监控

* 限流处理
* 服务降级

> 安全防护

* 鉴权处理
* 监控
* 机器网络隔离

主流网管
zuul：是Netflix开源的微服务网关，和Eureka,Ribbon,Hystrix等组件配合使用，Zuul 2.0比1.0的性能提高很多
kong: 由Mashape公司开源的，基于Nginx的API gateway
nginx+lua：是一个高性能的HTTP和反向代理服务器,lua是脚本语言，让Nginx执行Lua脚本，并且高并发、非阻塞的处理各种请求


### SpringCloud的网关组件zuul基本使用

> 加入依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
</dependency>
```

> 启动类加入注解

`@EnableZuulProxy`
默认集成断路器`@EnableCircuitBreaker`

> 自定义路由转发

```yml
zuul:
  routes:
    order-service: /apigateway/**
```

> 访问`order-service`服务的`getProductInfo``controller`

```
http://localhost:9000/apigateway/getProductInfo
```

### 高级篇幅之Zuul常用问题分析和网关过滤器原理分析

#### 路由名称定义问题

```yml
zuul:
  routes:
    order-service: /apigateway/**
    product-service: /apigateway/**
```
以上配置会覆盖掉`order-service`，值不能重复。

#### 处理Http请求头过滤掉cookie问题

```yml
zuul:
  routes:
    order-service: /apigateway/**
    product-service: /apigateway/product/**
  # 统一入口为上面的配置，其他入口忽略
  ignored-patterns: /*-service/**
  # 处理http请求头为空的问题
  sensitive-headers:
```  

#### 过滤器执行顺序问题 ，过滤器的order值越小，越先执行

#### 共享RequestContext，上下文对象