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

过滤器执行流程

![网关zuul-a](https://volc1612.gitee.io/blog/images/网关zuul/网关zuul-a.png)

#### 共享RequestContext，上下文对象

### 自定义Zuul过滤器实现登录鉴权实战

	简介：自定义Zuul过滤器实现登录鉴权实战

	1、新建一个filter包

	2、新建一个类，实现ZuulFilter，重写里面的方法

	3、在类顶部加注解，@Component,让Spring扫描

```java
import com.netflix.zuul.ZuulFilter;
import com.netflix.zuul.context.RequestContext;
import com.netflix.zuul.exception.ZuulException;
import org.apache.commons.lang.StringUtils;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Component;

import javax.servlet.http.HttpServletRequest;

import static org.springframework.cloud.netflix.zuul.filters.support.FilterConstants.PRE_TYPE;

/**
 * 登录过滤器
 */
@Component
public class LoginFilter extends ZuulFilter {

    /**
     * 过滤器类型，前置过滤器
     *
     * @return
     */
    @Override
    public String filterType() {
        return PRE_TYPE;
    }

    /**
     * 过滤器顺序，越小越先执行
     *
     * @return
     */
    @Override
    public int filterOrder() {

        return 4;
    }


    /**
     * 过滤器是否生效
     *
     * @return
     */
    @Override
    public boolean shouldFilter() {

        RequestContext requestContext = RequestContext.getCurrentContext();
        HttpServletRequest request = requestContext.getRequest();

        //System.out.println(request.getRequestURI()); ///apigateway/product/api/v1/product/list
        //System.out.println(request.getRequestURL()); //http://localhost:9000/apigateway/product/api/v1/product/list

        //ACL

        if ("/apigateway/order/api/v1/order/save".equalsIgnoreCase(request.getRequestURI())) {
            return true;
        } else if ("/apigateway/order/api/v1/order/list".equalsIgnoreCase(request.getRequestURI())) {
            return true;
        } else if ("/apigateway/order/api/v1/order/find".equalsIgnoreCase(request.getRequestURI())) {
            return true;
        }

        return false;
    }

    /**
     * 业务逻辑
     *
     * @return
     * @throws ZuulException
     */
    @Override
    public Object run() throws ZuulException {

        //JWT
        RequestContext requestContext = RequestContext.getCurrentContext();
        HttpServletRequest request = requestContext.getRequest();

        //token对象
        String token = request.getHeader("token");

        if (StringUtils.isBlank((token))) {
            token = request.getParameter("token");
        }


        //登录校验逻辑  根据公司情况自定义 JWT
        if (StringUtils.isBlank(token)) {
            // 是否继续执行网关操纵
            requestContext.setSendZuulResponse(false);
            // 返回错误码
            requestContext.setResponseStatusCode(HttpStatus.UNAUTHORIZED.value());
        }

        return null;
    }
}
```

### 高并发情况下接口限流

只对一个接口限流，每秒1000的并发，超过1000则返回太多请求的结果

```java
import com.google.common.util.concurrent.RateLimiter;
import com.netflix.zuul.ZuulFilter;
import com.netflix.zuul.context.RequestContext;
import com.netflix.zuul.exception.ZuulException;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Component;
import javax.servlet.http.HttpServletRequest;
import static org.springframework.cloud.netflix.zuul.filters.support.FilterConstants.PRE_TYPE;
/**
 * 订单限流
 */
@Component
public class OrderRateLimiterFilter extends ZuulFilter {


    //每秒产生1000个令牌
    private static final RateLimiter RATE_LIMITER = RateLimiter.create(1000);

    @Override
    public String filterType() {
        return PRE_TYPE;
    }

    @Override
    public int filterOrder() {
        return -4;
    }


    @Override
    public boolean shouldFilter() {


        RequestContext requestContext = RequestContext.getCurrentContext();
        HttpServletRequest request = requestContext.getRequest();

        //只对订单接口限流
        if ("/apigateway/order/api/v1/order/save".equalsIgnoreCase(request.getRequestURI())) {
            return true;
        }

        return false;
    }

    @Override
    public Object run() throws ZuulException {
        RequestContext requestContext = RequestContext.getCurrentContext();
        // 尝立即获取一个令牌
        if (!RATE_LIMITER.tryAcquire()) {
            requestContext.setSendZuulResponse(false);
            requestContext.setResponseStatusCode(HttpStatus.TOO_MANY_REQUESTS.value());
        }
        return null;
    }
}
```

### 网关Zuul集群搭建

