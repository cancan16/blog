---
title: 微服务核心知识分布式配置中心Config
date: 2019-07-18 22:07:25
categories: SPRINGCLOUD
tags: [分布式配置中心]
---

### 微服务下的分布式配置中心

简介：讲解什么是配置中心及使用前后的好处 (画图)
什么是配置中心：统一管理配置, 快速切换各个环境的配置

相关产品：
百度的disconf 
    地址:https://github.com/knightliao/disconf

阿里的diamand
    地址：https://github.com/takeseem/diamond

springcloud的configs-server:
    地址：http://cloud.spring.io/spring-cloud-config/
推荐干货文章：http://jm.taobao.org/2016/09/28/an-article-about-config-center/

![微服务核心知识分布式配置中心Config-a](https://volc1612.gitee.io/blog/images/微服务核心知识分布式配置中心Config/微服务核心知识分布式配置中心Config-a.png)
<!-- more -->


### SpringCloud的配置中心组件config-server

启动类增加注解

```
@EnableConfigServer
```

### 使用git服务器结合Config搭建分布式配置中心

config-server yml文件配置git仓库

```yml
#git配置
spring:
  cloud:
    config:
      server:
        git:
          uri: https://gitee.com/volc1612/config_cloud
          username: 916215796@qq.com
          password: volC9162157960
          # 超时时间
          timeout: 5
          # 分支
          default-label: master
```

* `config_cloud`仓库中添加`product-service`.yml文件

```yml
server:
  port: 8771
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
spring:
  application:
    # 使用ribbon时服务名称不能有下划线
    name: product-service
  server:
    enable-self-preservation: false
```

![微服务核心知识分布式配置中心Config-b](https://volc1612.gitee.io/blog/images/微服务核心知识分布式配置中心Config/微服务核心知识分布式配置中心Config-b.png)

* 验证`config-service`服务是否生效

重新启动`config-service`服务
访问地址
例子`http://localhost:9100/product-service.yml`

/{name}-{profiles}.properties
/{name}-{profiles}.yml
/{name}-{profiles}.json
/{label}/{name}-{profiles}.yml

`http://localhost:9100/product-service.yml`
`http://localhost:9100/product-service.properties`
`http://localhost:9100/product-service.json`
`http://localhost:9100/master/product-service.json`
会自动装换数据格式

### 分布式配置中心客户端使用实战

简介：微服务里面客户端接入配置中心实战
官方文档：http://cloud.spring.io/spring-cloud-config/single/spring-cloud-config.html#_spring_cloud_config_client
		
* 加入依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

* 修改对应服务的配置文件,把`application.yml`改为`bootstrap.yml`

```yml
#指定注册中心地址
eureka:
    client:
    serviceUrl:
        defaultZone: http://localhost:8761/eureka/

#服务的名称
spring:
  application:
    # 使用ribbon时服务名称不能有下划线
    name: product-service
    #指定从哪个配置中心读取
  cloud:
    config:
      discovery:
        service-id: CONFIG-SERVICE
        enabled: true
      # 配置文件环境
      profile: test
      # 建议用lable去区分环境，默认是lable是master分支
      label: master
```

注意点：
    1.配置文件要用bootstrap.yml
    2.默认读取文件名是 服务名称


