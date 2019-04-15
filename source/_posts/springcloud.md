---
title: springcloud
date: 2018-11-04 14:29:15
update: 2018-11-04 14:29:15
categories: SPRINGCLOUD
tags: [springcloud]
---

### springcloud

![springclouod_0](https://volc1612.gitee.io/blog/images/springcloud/springcloud_0.jpg)

依赖关系图：

![springclouod_b](https://volc1612.gitee.io/blog/images/springcloud/springcloud_b.png)

[SpringCloud学习网站][2]。

<!-- more -->

springcloud2.0要求JDK1.8及以上，以为springcloud2.0是基于springboot2.0开发的，而springboot2.0要求JDK版本在1.8及以上。

### 网站架构演变

项目：
包含业务逻辑层，视图层。前台项目和后台项目。
服务：
只包含业务逻辑层，没有视图层。

从传统架构(单点应用) -> 分布式架构(以项目进行拆分) -> SOA(Service-Oriented Architecture)架构(面向服务架构) -> 微服务架构 -> ?

* 传统架构

包括SSH或SSM架构，它们属于单点应用。传统架构的缺点是耦合度太高，一旦某个模块出现问题，可能有影响其他模块。

* 集群

相同项目部署在不同服务器上


* 分布式架构

不同模块部署在不同服务器上；
分布式解决网站高并发带来问题；
分布式架构基于传统架构演变而来，将传统项目拆分开多个子项目。每个项目中都有自己独立的数据库。分布式架构和传统架构区别在于，项目粒度更加精细，慢慢适合于互联网公司开发，耦合度降低。区分是不是分布式项目在于是不是有多个JVM进行通讯。不同模块部署在不同服务器上。


* SOA架构与微服务架构

SOA(Service-Oriented Architecture)架构是从分布式架构演变而来。

![springcloud_frame_soa_a](https://volc1612.gitee.io/blog/images/springcloud/springcloud_frame_soa_a.png)

微服务是一种服务化架构风格。通过将功能分散到各个离散的服务中以实现对解决方案的解耦。
服务的概念： 将共同的业务逻辑进行拆分，拆分成独立的项目进行部署。每个项目都没有视图层。
SOA架构，面向于服务架构，俗称服务化。将共同的业务代码进行抽取出来，提供其他接口进行调用，服务和服务之间采用`RPC`远程调用技术实现。

webservice: 底层采用HTTP协议和XML(SOAP)。

RPC: 两个或多个应用实现远程调用。

SOA特点： 底层基于`SOAP`或`ESB`(消息总线)实现。底层采用`HTTP`或者`HTTPS`协议，加上重量级的XML数据交换格式进行通讯。
在后面微服务中已`json`数据格式替换`xml`数据格式。



### 微服务架构产生原因

SOA的缺点：

1. 依赖于中心化发现机制
2. SOA架构采用SOAP(http+xml)，而xml数据格式比较占用宽带，整个xml报文中有非常大的冗余数据，所以在微服务架构中已json替换掉SOA中的xml数据传输格式。
3. SOA缺少服务管理和治理。


分布式和微服务区别：

* 微服务是架构设计方式，分布式更像是系统部署方式，两者概念不同。
* 微服务架构的服务粒度更加精细，独立数据库。
* 微服务架构更加体现轻量级，采用restful风格提供API。
* 开发更加敏捷，快速迭代产品。

微服务架构和SOA架构的区别

1. 微服务架构基于 SOA架构 演变过来，继承 SOA架构的优点，在微服务架构中去除 SOA 架构中的 ESB 消息总线，采用 http+json（restful）进行传输。
2. 微服务架构比 SOA 架构粒度会更加精细，让专业的人去做专业的事情（专注），目的提高效率，每个服务于服务之间互不影响，微服务架构中，每个服务必须独立部署，微服务架构更加轻巧，轻量级。
3. SOA 架构中可能数据库存储会发生共享，微服务强调独每个服务都是单独数据库，保证每个服务于服务之间互不影响。
4. 项目体现特征微服务架构比 SOA 架构更加适合与互联网公司敏捷开发、快速迭代版本，因为粒度非常精细。


微服务优缺点：

往微服务架构迁移有助于将大型的系统拆分为更小的组件，从而实现对系统架构的重新掌控。这些独立的组件更加易于管理、替换、部署、扩展和测试。采用新的技术，如新语言、框架或数据库，也会变得更加容易，因为它们可以用于较少的一些组件中，而不必一次性地用到整个系统之中。但是，采用微服务也会带来其他的影响。这些小组件之间的通信会大幅增加。实际上，组织必须要意识到往微服务的迁移就意味着往分布式系统进行迁移，在得到它所有收益的同时，也伴随着分布式系统的所有缺点，包括必须要处理延迟、认证与授权、无法抵达的消息。 

### SpringCloud

![springcloud_a](https://volc1612.gitee.io/blog/images/springcloud/springcloud_a.png)

### 服务注册与发现

服务治理： 在传统的`RPC`远程框架中，管理每个服务于服务之间依赖关系。
注册中心： 存放服务地址相关信息(接口地址)。
服务注册与发现的原理： 在任何`RPC`远程调用框架中都有一个注册中心。
SpringCloud支持以下三种注册中心： `Eureka`、`Consul`(Go言语编写)、`Zookeeper`。
Dubbo支持两种注册中心： `Redis`和`Zookpeeper`。

请求经过注册中心、生产者和消费者的过程：

* 启动注册中心。
* 启动生产者服务
* 生产者服务在启动时会把当前服务的信息，如ip地址和端口号以及别名注册到注册中心。注册中心会把服务信息缓存道`JVM`中，默认情况下`eureka`每隔30秒更新一次注册过的服务信息。
* 消费者在调用接口的时候会使用服务别名`serviceid`去注册中心上获取实际`RPC`远程调用地址。
* 如果消费者发现实际的`RPC`远程调用地址，再使用本地的HttpClient技术实现调用。

服务注册： 将服务中心注册到服务中心上(生产服务)。
服务发现： 从服务中心获取服务信息(消费者)。

#### 搭建注册中心Eureka

搭建springcloud2.0版本Eureka服务注册中心

##### 引入依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.itmayiedu</groupId>
    <artifactId>springcloud-2.0-eureka-server</artifactId>
    <version>1.0-SNAPSHOT</version>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.1.RELEASE</version>
    </parent>
    <!-- 管理依赖 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.M7</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <!--SpringCloud eureka-server 依赖包 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
    </dependencies>
    <!-- 注意： 这里必须要添加， 否者各种依赖有问题 -->
    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/libs-milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>
</project>
```

##### 配置application.yml

```bash
### 服务端口号
server:
  port: 8100
### eureka 基本信息配置
eureka:
  instance:
    ### 注册到eurekaip地址
    hostname: 127.0.0.1
  client:
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
    ### 因为自己是为注册中心，不需要自己注册自己(集群时为true)
    register-with-eureka: false
    ### 因为自己是为注册中心，不需要检索服务(集群时为true)
    fetch-registry: false
```

##### 启动类

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;
/**
 * @description: eureka测试启动类
 * @author: liucancan
 * @create: 2018-11-03 16:29
 **/
@SpringBootApplication
// 开启Eurekaserver服务注解，开启注册中心
@EnableEurekaServer
public class AppEureka {
    public static void main(String[] args) {
        SpringApplication.run(AppEureka.class, args);
    }
}
```

![springcloud_eureka_a](https://volc1612.gitee.io/blog/images/springcloud/springcloud_eureka_a.png)

#### 创建服务者(服务注册)

##### 引入依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.itmayiedu</groupId>
    <artifactId>springcloud-2.0-member</artifactId>
    <version>1.0-SNAPSHOT</version>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.1.RELEASE</version>
    </parent>
    <!-- 管理依赖 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.M7</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- SpringBoot整合eureka客户端 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
    </dependencies>
    <!-- 注意： 这里必须要添加， 否者各种依赖有问题 -->
    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/libs-milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>
</project>
```

##### application.yml

```bash
### 服务启动端口号
server:
  port: 8000
### 服务名称(服务注册到eureka名称)，服务注册到哦注册中心的服务名称
spring:
  application:
    name: app-itmayiedu-member
### 服务注册到eureka地址
eureka:
  client:
    service-url:
      # 当前服务注册到eureka服务的地址
      defaultZone: http://localhost:8100/eureka
    ### 因为该应用为注册中心，不会注册自己
    register-with-eureka: true
    ### 是否需要从eureka上获取注册信息，检索服务
    fetch-registry: true
```

##### 服务者启动类

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

/**
 * @description: eureka测试启动类
 * @author: liucancan
 * @create: 2018-11-03 16:29
 **/
@SpringBootApplication
// 将该服务注册到eureka注册中心
@EnableEurekaClient
public class AppMember {

    public static void main(String[] args) {
        SpringApplication.run(AppMember.class, args);
    }
}
```

##### 服务提供资源

```java
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @description: 会员服务接口
 * @author: liucancan
 * @create: 2018-11-03 17:54
 **/
@RestController
public class MemberController {

    @RequestMapping("/getMember")
    public String getMember() {
        return "springcloud2.0会员服务";
    }
}
```

#### 使用服务资源

启动注册中心

![springcloud_eureka_b](https://volc1612.gitee.io/blog/images/springcloud/springcloud_eureka_b.png)

使用服务者资源

![springcloud_eureka_c](https://volc1612.gitee.io/blog/images/springcloud/springcloud_eureka_c.png)

### 使用RestTemplate对象实现本地负载均衡

`RestTemplate`整合了ribbon负载均衡器，底层采用httpclient，ribbon实现了本地负载均衡，默认轮询策略。不像nginx应用服务器的负载均衡。

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

/**
 * @description: 订单服务启动类
 * @author: liucancan
 * @create: 2018-11-03 16:29
 **/
@SpringBootApplication
// 将该服务注册到eureka注册中心
@EnableEurekaClient
public class AppOrder {

    public static void main(String[] args) {
        SpringApplication.run(AppOrder.class, args);
    }

    // 注入RestTemplate
    @Bean
    // 这个RestTemplate在请求时拥有客户端负载均衡的能力
    @LoadBalanced
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}

```

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

/**
 * @description: 订单服务接口
 * @author: liucancan
 * @create: 2018-11-03 17:54
 **/
@RestController
public class OrderController {

    // RestTemplate是由springboot提供，默认整合了ribbon负载均衡器，底层采用httpclient
    @Autowired
    private RestTemplate restTemplate;

    /**
     * 订单服务调用会员服务
     *
     * @return
     */
    @RequestMapping("/getOrder")
    public String getOrder() {
        // 订单服务调用会员服务有两种调用方式，一种是采用别名，一种是直接调用
//        String result = restTemplate.getForObject("http://localhost:8000/getMember", String.class);
//        System.out.println("订单服务调用会员服务结果==：" + result);
        // 使用别名调用
        String url = "http://app-itmayiedu-member/getMember";
        String result2 = restTemplate.getForObject(url, String.class);
        System.out.println("订单服务调用会员服务结果==：" + result2);
        return result2;
    }
}
```

使用`RestTemplate`作为负载均衡的条件

- 多项目有serviceid，并且注入到Eureka中；
- 注入RestTemplate时需要@LoadBalanced注解开启客户端负载均衡能力；
- 调用者需要使用`serviceid`(服务名)方式调用其他服务；

### Eureka高可用

在微服务中RPC远程调用框架最核心的是服务的治理。
如果注册中心因为某种原因出现故障，会导致整个微服务环境不可用
解决办法： 搭建注册中心集群，大型互联网注册中心中都是集群的。Eureka搭建集群原理是`相互注册`的原理，形成一组相互注册的注册中心，从而实现数据的相互同步，达到高可用的效果。

Eureka1

```bash
### 服务端口号
server:
  port: 8100
### eureka 基本信息配置
### 注册中心集群一定要定义相同的服务名称(服务注册到eureka名称)，服务注册到哦注册中心的服务名称
spring:
  application:
    name: app-itmayiedu-eureka
eureka:
  instance:
    ### 注册到eurekaip地址
    hostname: 127.0.0.1
  client:
    serviceUrl:
      # 多个用逗号 http://${eureka.instance.hostname}:${server.port}/eureka/,http://${eureka.instance.hostname}:${server.port}/eureka/
      defaultZone: http://${eureka.instance.hostname}:9100/eureka/
    ### 因为自己是为注册中心，不需要自己注册自己(注册中心集群时为true)
    register-with-eureka: true
    ### 因为自己是为注册中心，不需要检索服务(注册中心集群时为true)
    fetch-registry: true
```

Eureka2

```bash
### 服务端口号
server:
  port: 8100
### eureka 基本信息配置
### 注册中心集群一定要定义相同的服务名称(服务注册到eureka名称)，服务注册到哦注册中心的服务名称
spring:
  application:
    name: app-itmayiedu-eureka
eureka:
  instance:
    ### 注册到eurekaip地址
    hostname: 127.0.0.1
  client:
    serviceUrl:
      # 多个用逗号 http://${eureka.instance.hostname}:${server.port}/eureka/,http://${eureka.instance.hostname}:${server.port}/eureka/
      defaultZone: http://${eureka.instance.hostname}:9100/eureka/
    ### 因为自己是为注册中心，不需要自己注册自己(注册中心集群时为true)
    register-with-eureka: true
    ### 因为自己是为注册中心，不需要检索服务(注册中心集群时为true)
    fetch-registry: true
```

Eureka1服务和Eureka2服务做集群

1. `name: app-itmayiedu-eureka`注册中心集群服务名称都设置相同
2. `defaultZone: http://${eureka.instance.hostname}:9100/eureka/`注册到另外集群注册中心的服务地址，多个用`,`隔开
3. `register-with-eureka: true`注册本服务到注册中心
4. `fetch-registry: true`能够检索到本服务

![springcloud_eureka_d](https://volc1612.gitee.io/blog/images/springcloud/springcloud_eureka_d.png)

#### 客户端注册到集群注册中心

`defaultZone`多个注册中心地址

```bash
### 服务启动端口号
server:
  port: 8000
### 服务名称(服务注册到eureka名称)，服务注册到哦注册中心的服务名称
spring:
  application:
    name: app-itmayiedu-member
### 服务注册到eureka地址
eureka:
  client:
    service-url:
      # 当前服务注册到eureka服务的地址
      defaultZone: http://localhost:8100/eureka,http://localhost:9100/eureka
    ### 因为该应用为注册中心，不会注册自己
    register-with-eureka: true
    ### 是否需要从eureka上获取注册信息，检索服务
    fetch-registry: true
```

![springcloud_eureka_e](https://volc1612.gitee.io/blog/images/springcloud/springcloud_eureka_e.png)

### Eureka注册中心自我保护机制

EurekaServer服务端默认是开启自我保护机制的。[请参考`Eureka`自我保护自己][1]

`EurekaClient`注册中心客户端和，EurekaServer如果注册中心服务端，在网络和其他原因导致客户端和服务端不能通讯，而Eureka注册中心不会对EurekaClient服务剔除。

自我保护机制： 在运行期间会去统计心跳失败比例在 15 分钟之内是否低于 85%，如果低于 85%，Eureka Server 会将这些实例保护起来，让这些实例不会过期。目的是为了防止网络问题，导致Eureka服务端误把EurekaClient端剔除。我们在单机测试的时候很容易满足心跳失败比例在 15 分钟之内低于 85%，这个时候就会触发 Eureka 的保护机制，一旦开启了保护机制，则服务注册中心维护的服务实例就不是那么准确了

自我保护模式被激活的条件是：在 1 分钟后，`Renews (last min)` < `Renews threshold`。

这两个参数的意思：

`Renews threshold`：`Eureka Server`期望每分钟收到客户端实例续约的总数。
`Renews (last min)`：`Eureka Server`最后`1`分钟收到客户端实例续约的总数。

![springcloud_eureka_f](https://volc1612.gitee.io/blog/images/springcloud/springcloud_eureka_f.png)

在什么环境下开启自我保护机制？

本地环境： 建议关闭
生产环境： 建议打开

#### 关闭Eureka注册中心自我保护机制

EurekaServer端配置

```bash
eureka
  server:
    # 测试时关闭自我保护机制，保证不可用服务及时踢出
    enable-self-preservation: false
    # 表示无心跳包后间隔2秒中剔除
    eviction-interval-timer-in-ms: 2000
```

EurekaClient端配置

```bash
eureka:
  instance:
    ### Eureka客户端向服务端发送心跳的时间间隔，单位为秒（客户端告诉服务端自己会按照该规则，每隔1秒发送一次心跳包
    lease-renewal-interval-in-seconds: 1
    #### Eureka服务端在收到最后一次心跳之后等待的时间上限，单位为秒，超过则剔除（客户端告诉服务端按照此规则等待自己）
    lease-expiration-duration-in-seconds: 2
```

***需要注意：***
Eureka客户端如果没有设置发送心跳包时间间隔，默认是30秒获取Eureka注册中心的服务列表。

### 使用Zookeeper替代Eureka作为注册中心

`Zookeeper`分布式协调工具，实现注册中心，采用临时节点类型。
在window环境演示`Zookeeper`代替`Eureka`。

下载`zookeeper`

目录结构：

![springcloud_zookeeper_a](https://volc1612.gitee.io/blog/images/springcloud/springcloud_zookeeper_a.png)

启动zookeeper,执行bin/zkServer.cmd

```bash
cd zookeeper-3.3.6\bin
zkServer.cmd
```

启动
![springcloud_zookeeper_c](https://volc1612.gitee.io/blog/images/springcloud/springcloud_zookeeper_c.png)

使用`zookeeper客户端ZooInspector`连接`zookeeper服务端`

![springcloud_zookeeper_d](https://volc1612.gitee.io/blog/images/springcloud/springcloud_zookeeper_d.png)

![springcloud_zookeeper_e](https://volc1612.gitee.io/blog/images/springcloud/springcloud_zookeeper_e.png)

#### 把服务注入到zookeeper

依赖包

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.itmayiedu</groupId>
    <artifactId>springboot2.0-zookeeper_member</artifactId>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.1.RELEASE</version>
    </parent>
    <!-- 管理依赖 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.M7</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- SpringBoot整合zookeeper客户端 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
        </dependency>

    </dependencies>
    <!-- 注意： 这里必须要添加， 否者各种依赖有问题 -->
    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/libs-milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>

</project>
```

配置文件

```bash
###订单服务的端口号
server:
  port: 8002
###服务别名----服务注册到注册中心名称
spring:
  application:
    name: zk-member
  cloud:
    zookeeper:
      connect-string: 127.0.0.1:2181
```      

项目启动类

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

/**
 * @description: zookeeper测试启动类
 * @author: liucancan
 * @create: 2018-11-03 16:29
 **/
@SpringBootApplication
// 该注解的作用是向非Eureka的注册中心中注册该服务，如Zookeeper，Consul等
@EnableDiscoveryClient
public class AppMember {

    public static void main(String[] args) {
        SpringApplication.run(AppMember.class, args);
    }
}

```

`@EnableDiscoveryClient`注解的使用作用是把服务注入到非Eureka为注册中心的注册中心中。

启动服务后在`ZooInspector`客户端刷新查看，已把`zk-member`注入到`zookeeper`中。

![springcloud_zookeeper_f](https://volc1612.gitee.io/blog/images/springcloud/springcloud_zookeeper_f.png)

`Zookeeper`和`Eureka`区别在于，`Zookeeper`没有服务保护机制，只要服务和`Zookeeper`没有通讯成功，`Zookeeper`就会把该服务节点删除。

### 使用Consul代替Eureka作为注册中心

Consul 是一套开源的分布式服务发现和配置管理系统，由 HashiCorp 公司用 Go 语言开发。
它具有很多优点。包括： 基于 raft 协议，比较简洁； 支持健康检查, 同时支持 HTTP 和 DNS 协议 支持跨数据中心的 WAN 集群 提供图形界面 跨平台，支持 Linux、Mac、Windows。

<a href="https://springcloud.cc/spring-cloud-consul.html">Consul 整合SpringCloud 学习网站</a>

<a href="https://www.consul.io/downloads.html">Consul下载地址</a>

#### win环境使用Consul

官方下载地址下载window版，解压得到一个可执行文件。 
设置环境变量，让我们直接在cmd里可直接使用consul使命。在系统变量path后面添加consul所在目录。例如D:\soft\consul_1.1.0_windows_amd64

启动consul命

```bash
C:\Users\Admin>consul agent -dev -ui -node=cy
```
-dev开发服务器模式启动，-node结点名为cy，-ui可以用界面访问，默认能访问。

```bash
C:\Users\Admin>consul agent -dev -ui -node=cy
==> Starting Consul agent...
==> Consul agent running!
           Version: 'v1.2.2'
           Node ID: 'abe19393-98e3-5e61-8b1a-5d27b84e5c0f'
         Node name: 'cy'
        Datacenter: 'dc1' (Segment: '<all>')
            Server: true (Bootstrap: false)
       Client Addr: [127.0.0.1] (HTTP: 8500, HTTPS: -1, DNS: 8600)
      Cluster Addr: 127.0.0.1 (LAN: 8301, WAN: 8302)
           Encrypt: Gossip: false, TLS-Outgoing: false, TLS-Incoming: false

==> Log data will now stream in as it occurs:

    2018/11/09 10:56:35 [DEBUG] agent: Using random ID "abe19393-98e3-5e61-8b1a-
5d27b84e5c0f" as node ID
    2018/11/09 10:56:35 [INFO] raft: Initial configuration (index=1): [{Suffrage
:Voter ID:abe19393-98e3-5e61-8b1a-5d27b84e5c0f Address:127.0.0.1:8300}]
    2018/11/09 10:56:35 [INFO] raft: Node at 127.0.0.1:8300 [Follower] entering
Follower state (Leader: "")
    2018/11/09 10:56:35 [INFO] serf: EventMemberJoin: cy.dc1 127.0.0.1
    2018/11/09 10:56:35 [INFO] serf: EventMemberJoin: cy 127.0.0.1
    2018/11/09 10:56:35 [INFO] agent: Started DNS server 127.0.0.1:8600 (udp)
    2018/11/09 10:56:35 [INFO] consul: Adding LAN server cy (Addr: tcp/127.0.0.1
:8300) (DC: dc1)
    2018/11/09 10:56:35 [INFO] consul: Handled member-join event for server "cy.
dc1" in area "wan"
    2018/11/09 10:56:35 [DEBUG] agent/proxy: managed Connect proxy manager start
ed
    2018/11/09 10:56:35 [INFO] agent: Started DNS server 127.0.0.1:8600 (tcp)
    2018/11/09 10:56:35 [INFO] agent: Started HTTP server on 127.0.0.1:8500 (tcp
)
    2018/11/09 10:56:35 [INFO] agent: started state syncer
    2018/11/09 10:56:35 [WARN] raft: Heartbeat timeout from "" reached, starting
 election
    2018/11/09 10:56:35 [INFO] raft: Node at 127.0.0.1:8300 [Candidate] entering
 Candidate state in term 2
    2018/11/09 10:56:35 [DEBUG] raft: Votes needed: 1
    2018/11/09 10:56:35 [DEBUG] raft: Vote granted from abe19393-98e3-5e61-8b1a-
5d27b84e5c0f in term 2. Tally: 1
    2018/11/09 10:56:35 [INFO] raft: Election won. Tally: 1
    2018/11/09 10:56:35 [INFO] raft: Node at 127.0.0.1:8300 [Leader] entering Le
ader state
    2018/11/09 10:56:35 [INFO] consul: cluster leadership acquired
    2018/11/09 10:56:35 [INFO] consul: New leader elected: cy
    2018/11/09 10:56:36 [INFO] connect: initialized CA with provider "consul"
    2018/11/09 10:56:36 [DEBUG] consul: Skipping self join check for "cy" since
the cluster is too small
    2018/11/09 10:56:36 [INFO] consul: member 'cy' joined, marking health alive
    2018/11/09 10:56:36 [DEBUG] agent: Skipping remote check "serfHealth" since
it is managed automatically
    2018/11/09 10:56:36 [INFO] agent: Synced node info
    2018/11/09 10:56:38 [DEBUG] agent: Skipping remote check "serfHealth" since
it is managed automatically
    2018/11/09 10:56:38 [DEBUG] agent: Node info in sync
    2018/11/09 10:56:38 [DEBUG] agent: Node info in sync
```

浏览器测试访问地址: http://localhost:8500 

![springcloud_consul_a](https://volc1612.gitee.io/blog/images/springcloud/springcloud_consul_a.png)

#### 服务注册到consul注册中心

依赖包

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.itmayiedu</groupId>
    <artifactId>springboot2.0-consul_member</artifactId>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.1.RELEASE</version>
    </parent>
    <!-- 管理依赖 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.M7</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--SpringCloud consul-server -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
        </dependency>
    </dependencies>
    <!-- 注意： 这里必须要添加， 否者各种依赖有问题 -->
    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/libs-milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>
</project>
```

配置文件

```bash
### 会员服务端口号
server:
  port: 8502
spring:
  application:
    # 注册到consul注册中心的服务名称
    name: consul-member
  #### consul注册中心地址配置
  cloud:
    consul:
      host: localhost
      # consul注册中心端口号
      port: 8500
      discovery:
        # 注册中心默认情况下注册到consul注册中心的服务地址，为计算机名称例如：PC-20181109，这里指定服务的地址为ip地址
        hostname: 192.168.18.220
```

启动类

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

/**
 * @description: 会员服务启动类
 * @author: liucancan
 * @create: 2018-11-03 16:29
 **/
@SpringBootApplication
// 该注解的作用是向非Eureka的注册中心中注册该服务，如Zookeeper，Consul等
@EnableDiscoveryClient
public class AppMember {

    public static void main(String[] args) {
        SpringApplication.run(AppMember.class, args);
    }
}
```

这时通浏览器查看服务已经注册到consul注册中心了。

![springcloud_consul_b](https://volc1612.gitee.io/blog/images/springcloud/springcloud_consul_b.png)

将Eureka注册中心换成其他注册中心时，客户端通过远程调用RPC方式仍不变。无非变化一些配置文件和地址，和maven的依赖信息。

### 使用DiscoveryClient获取到注册中的服务列表信息

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

/**
 * @description: 会员服务接口
 * @author: liucancan
 * @create: 2018-11-03 17:54
 **/
@RestController
public class MemberController {

    @Autowired
    private DiscoveryClient discoveryClient;

    /**
     * 使用 DiscoveryClient 获取注册中心的服务列表
     * @return
     */
    @RequestMapping("/getServiceInstance")
    public List<ServiceInstance> getServiceInstance() {
        List<ServiceInstance> instances = discoveryClient.getInstances("consul-member");
        for (ServiceInstance serviceInstance : instances) {
            System.out.println(serviceInstance);
        }
        return instances;
    }
}

```

获取consul注册中心中的服务信息

```json
[
    {
        "serviceId": "consul-member",
        "host": "192.168.18.220",
        "port": 8502,
        "secure": false,
        "metadata": {},
        "uri": "http://192.168.18.220:8502",
        "scheme": null
    }
]
```

### 服务注册与发现的原理

* 服务端以别名的方式注入到注册中心，注册中心保存所有微服务的调用信息。
* 当有服务之间相互调用时，A服务端会已别名的方式去注册中心中查找B服务的实际接口地址。
* A服务获取到B服务接口的地址后，缓存大本服务的`JVM`中，然后在本地实现远程的`RPC`(底层httpclient实现)调用。

### 本地负载均衡和服务器端负载均衡

`Ribbon`是客户端负载均衡器，客户端服务从注册中心获取对应的服务列表后，缓存到`JVM`中，然后在本地使用`RPC`远程调用技术，即在本地实现负载均衡。

动态负载均衡算法： 总请求数`%`服务器数量=实际服务器下表位置。例如，B服务集群数为2，A服务端第一次请求B服务时： `1%2=1`获取list[1]=B服务器实例。

`Nginx`是服务器端负载均衡，所有客户端请求都会交给`Nginx`，然后再由`Nginx`实现转发请求。即服务器端实现负载均衡。

使用场景： 
`Ribbon`本地负载均衡适合微服务本地PRC远程调用，例如：Dubbo，springcloud。
`Nginx`适合于针对服务器端，例如：tomcat，jetty。


#### 模拟Ribbon本地负载均衡

`Ribbon`实现本地负载均衡原理，模拟A服务调用B服务实现本地负载均衡。

注册中心配置

```bash
### 服务端口号
server:
  port: 8100
### eureka 基本信息配置
### 注册中心集群一定要定义相同的服务名称(服务注册到eureka名称)，服务注册到哦注册中心的服务名称
spring:
  application:
    name: app-itmayiedu-eureka
eureka:
  instance:
    ### 注册到eurekaip地址
    hostname: 127.0.0.1
  client:
    serviceUrl:
      # 多个用逗号 http://${eureka.instance.hostname}:${server.port}/eureka/,http://${eureka.instance.hostname}:${server.port}/eureka/
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
    ### 因为自己是为注册中心，不需要自己注册自己(注册中心集群时为true)
    register-with-eureka: false
    ### 因为自己是为注册中心，不需要检索服务(注册中心集群时为true)
    fetch-registry: false
  server:
    # 测试时关闭自我保护机制，保证不可用服务及时踢出
    enable-self-preservation: false
    # 表示无心跳包后间隔2秒中剔除
    eviction-interval-timer-in-ms: 2000
```

B服务配置,分别启动8110、8111和8112端口号

```bash
### 会员服务启动端口号
server:
  port: 8112
### 服务名称(服务注册到eureka名称)，服务注册到哦注册中心的服务名称
spring:
  application:
    name: app-itmayiedu-member
### 服务注册到eureka地址
eureka:
  client:
    service-url:
      # 当前服务注册到eureka服务的地址
      defaultZone: http://localhost:8100/eureka
    ### 注册到注册中心
    register-with-eureka: true
    ### 是否需要从eureka上获取注册信息，检索服务
    fetch-registry: true
  instance:
    ### Eureka客户端向服务端发送心跳的时间间隔，单位为秒（客户端告诉服务端自己会按照该规则，每隔1秒发送一次心跳包
    lease-renewal-interval-in-seconds: 1
    #### Eureka服务端在收到最后一次心跳之后等待的时间上限，单位为秒，超过则剔除（客户端告诉服务端按照此规则等待自己）
    lease-expiration-duration-in-seconds: 2
```

A服务配置

```bash
### 订单服务启动端口号
server:
  port: 8120
### 服务名称(服务注册到eureka名称)，服务注册到哦注册中心的服务名称
spring:
  application:
    name: app-itmayiedu-order
### 服务注册到eureka地址
eureka:
  client:
    service-url:
      # 当前服务注册到eureka服务的地址
      defaultZone: http://localhost:8100/eureka
    ### 因为该应用为注册中心，不会注册自己
    register-with-eureka: true
    ### 是否需要从eureka上获取注册信息，检索服务
    fetch-registry: true
```

A服务启动类，关闭@LoadBalanced实现的本地负载均衡

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

/**
 * @description: 订单服务启动类
 * @author: liucancan
 * @create: 2018-11-03 16:29
 **/
@SpringBootApplication
// 将该服务注册到eureka注册中心
@EnableEurekaClient
public class AppOrder {

    public static void main(String[] args) {
        SpringApplication.run(AppOrder.class, args);
    }

    // 注入RestTemplate
    @Bean
    // 这个RestTemplate在请求时拥有客户端负载均衡的能力
//    @LoadBalanced
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}
```

实现`Ribbon`本地负载均衡

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import java.util.List;
import java.util.concurrent.atomic.AtomicInteger;

/**
 * @description: 模拟Ribbon的本地负载均衡，实现订单服务调用会员服务接口
 * @author: liucancan
 * @create: 2018-11-03 17:54
 **/
@RestController
public class ExtRibbonController {

    @Autowired
    private DiscoveryClient discoveryClient;

    @Autowired
    private RestTemplate restTemplate;

    // AtomicInteger原子操作类，线程安全，累加请求次数，默认为0
    private AtomicInteger atomicInteger = new AtomicInteger(0);

    /**
     * 订单服务调用会员服务
     *
     * @return
     */
    @RequestMapping("/getMember")
    public String getMember() {
        int reqCount = atomicInteger.addAndGet(1);
        // 从注册中心中获取会员服务集群地址
        List<ServiceInstance> instances = discoveryClient.getInstances("app-itmayiedu-member");
        if (null != instances && instances.size() > 0) {
            ServiceInstance serviceInstance = instances.get(reqCount % instances.size());
            return serviceInstance.getUri() + " " + restTemplate.getForObject(serviceInstance.getUri() + "/getMember", String.class);
        }
        return "请求失败";
    }
}
```

启动Eureka注册中心，启动B服务，启动A服务，通过A服务多次访问B服务查看效果，以实现负载均衡。

### Feign客户端

在`SpringCloud`中支持两种客户端调用工具，`Rest`和`Feign`。`Feign`客户端是声明式调用工具，采用接口和注解方式实现，易读性比较强。

### 微服务中使用Feign工具

***使用Feign工具调用微服务接口管理类***

`@FeignClient(name = "app-itmayiedu-member")`指定微服务的名称；
`@RequestMapping("/getMember")`添加微服务接口入口；

```java
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.RequestMapping;

/**
 * @description: feign客户端调用接口类
 * @author: liucancan
 * @create: 2018-11-11 15:03
 **/
// 指定服务名称
@FeignClient(name = "app-itmayiedu-member")
public interface MemberApiFeign {

    // 指定接口入口
    @RequestMapping("/getMember")
    String getMember();
}
```

***使用Feign接口类***

注入Feign接口类；

```java
import com.itmayiedu.feign.MemberApiFeign;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @description: 订单服务接口
 * @author: liucancan
 * @create: 2018-11-03 17:54
 **/
@RestController
public class FeignOrderController {

    // 注入Feign客户端
    @Autowired
    private MemberApiFeign memberApiFeign;

    /**
     * 订单服务调用会员服务
     *
     * @return
     */
    @RequestMapping("/getFeignOrder")
    public String getOrder() {
        return memberApiFeign.getMember();
    }
}
```

***项目中开启Feign客户端使用权限***

`@EnableFeignClients`

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.cloud.openfeign.EnableFeignClients;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

/**
 * @description: 订单服务启动类
 * @author: liucancan
 * @create: 2018-11-03 16:29
 **/
@SpringBootApplication
// 将该服务注册到eureka注册中心
@EnableEurekaClient
// 开启Feign客户端的权限
@EnableFeignClients
public class AppOrder {

    public static void main(String[] args) {
        SpringApplication.run(AppOrder.class, args);
    }

    // 注入RestTemplate
    @Bean
    // 这个RestTemplate在请求时拥有客户端负载均衡的能力
//    @LoadBalanced
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}
```

![springcloud_feign_a](https://volc1612.gitee.io/blog/images/springcloud/springcloud_feign_a.png)


### 使用Feign客户端工具项目代码架构

![springcloud_feign_b](https://volc1612.gitee.io/blog/images/springcloud/springcloud_feign_b.png)

**订单服务中使用Feign客户端工具调用会员服务**

订单服务`(springcloud-2.0-itmayiedu-api-order-service-impl)`引用订单接口`(springcloud-2.0-itmayiedu-api-order-service)`和会员接口`(springcloud-2.0-itmayiedu-api-member-service)`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>springcloud-2.0-itmayiedu-parent</artifactId>
        <groupId>com.itmayiedu</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.itmayiedu</groupId>
    <artifactId>springcloud-2.0-itmayiedu-api-order-service-impl</artifactId>
    <dependencies>
        <dependency>
            <artifactId>springcloud-2.0-itmayiedu-api-order-service</artifactId>
            <groupId>com.itmayiedu</groupId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <!-- S 引入会员接口 -->
        <dependency>
            <groupId>com.itmayiedu</groupId>
            <artifactId>springcloud-2.0-itmayiedu-api-member-service</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <!-- E 引入会员接口 -->
    </dependencies>
</project>
```

订单服务`(springcloud-2.0-itmayiedu-api-order-service-impl)`中使用`Feign`客户端工具，而不是在订单接口`(springcloud-2.0-itmayiedu-api-order-service)`中使用。

```java
import com.itmayiedu.api.service.impl.IMemberService;
import org.springframework.cloud.openfeign.FeignClient;

/**
 * @description: 会员接口feign客户端
 * @author: liucancan
 * @create: 2018-11-12 15:09
 **/
@FeignClient(name = "app-itmayiedu-member")
public interface MemberServiceFeign extends IMemberService {
}
```

### Feign客户端超时时间设置

当使用`feign`客户端调用时，底层使用ribbon本地负载均衡，而ribbon默认超时时间为`1`秒，当有些接口使用`feign`客户端调用时超过`1`秒后，会报`Read timed out`的异常。这时需要配置ribbon的请求和响应的超时时间。

```bash
### springcloud中feign客户端默认是开启ribbon的。设置feign客户端超时时间
ribbon:
  ### 指的是建立连接所用的时间，适用于网络状况正常的情况下，两端连接所用的时间。
  ReadTimeout: 5000
  ### 指的是建立连接后从服务器读取到可用资源所用的时间。
  ConnectTimeout: 5000
```

#### Feign客户端默认开启Ribbon本地负载均衡

使用`Feign`客户端调用接口，默认是开启了`Ribbon`本地负载均衡的。

### 服务雪崩效应

#### 什么是服务雪崩效应

tomcat默认有一个线程池在工作，如果请求数大于线程池的容量`maximum-pool-size`，有些请求就会延迟等待。

服务雪崩效应是一种因`服务提供者的不可用`（原因）导致`服务调用者不可用`（结果），并将不可用逐渐放大的现象。如下图所示：

![springcloud_xuebeng_a](https://volc1612.gitee.io/blog/images/springcloud/springcloud_xuebeng_a.png)

上图中, A为服务提供者, B为A的服务调用者, C和D是B的服务调用者. 当A的不可用,引起B的不可用,并将不可用逐渐放大C和D时, 服务雪崩就形成了。
服务雪崩的过程可以分为三个阶段：

* 服务提供者不可用；
* 重试加大请求流量；
* 服务调用者不可用；

![springcloud_xuebeng_b](https://volc1612.gitee.io/blog/images/springcloud/springcloud_xuebeng_b.bmp)

***应对策略***

![springcloud_xuebeng_c](https://volc1612.gitee.io/blog/images/springcloud/springcloud_xuebeng_c.bmp)

#### Hystrix服务保护框架

`Hystrix`能够帮助解决和实现`断路器`、`服务降级`、`服务雪崩效应`、`服务熔断`、`服务隔离机制`。

### 服务降级

在高并发的情况下，请求不能及时响应，防止用户一直等待就可以使用服务降级的处理方式(返回一个友好提示，不会去处理请求，调用fallback本地方法)，目的是为了用户体验。

### 服务熔断

服务熔断的目的是为了保护服务。在高并发的情况下，如果请求达到了一定的极限(阈值)，自动开启服务保护功能，使用服务降级的方式，返回友好提示。服务熔断和服务降级通常一起使用。服务熔断后一般会走服务降级的方法。

### 服务隔离

服务隔离有两种隔离机制，`线程池隔离`，`信号量隔离`。`线程池隔离`每个服务接口都有独立的线程池，但是占用服务器资源。


### 使用Hystrix

#### 在`parent`项目中引入`Hystrix`依赖

```xml
<!-- S hystrix依赖包 断路器 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
<!-- E hystrix依赖包 断路器 -->
```

#### 使用@HystrixCommand(fallbackMethod="")

解决服务雪崩效应使用`@HystrixCommand`注解，`@HystrixCommand`默认开启了线程池隔离机制和服务降级和服务熔断机制。`fallbackMethod`作用： `服务降级执行`。

#### 验证@HystrixCommand默认开启线程池隔离机制

服务配置开启`Hystrix`断路器功能，并设置关闭`Hystrix`服务保护超时时间(默认为1秒)。

```bash
feign:
  hystrix:
    # 服务开启hystrix断路器
    enabled: true
hystrix:
  command:
    default:
      execution:
        timeout:
          # 禁用Hystrix超时时间，生产环境不能禁用hystrix超时时间
          enabled: false
```

启动类添加注解`@EnableHystrix`服务保护机制

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.cloud.netflix.hystrix.EnableHystrix;
import org.springframework.cloud.openfeign.EnableFeignClients;

/**
 * @description: 订单接口
 * @author: liucancan
 * @create: 2018-11-03 16:29
 **/
@SpringBootApplication
// 开启Eurekaserver服务注解，开启注册中心
@EnableEurekaClient
@EnableFeignClients
// 开启服务保护机制
@EnableHystrix
public class AppOrder {
    public static void main(String[] args) {
        SpringApplication.run(AppOrder.class, args);
    }
}
```

访问资源

```java
import com.itmayiedu.api.service.impl.IOrderService;
import com.itmayiedu.base.BaseApiService;
import com.itmayiedu.base.ResponseBase;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import entity.UserEntity;
import itmayiedu.api.feign.MemberServiceFeign;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @description: 会员接口实现类
 * @author: liucancan
 * @create: 2018-11-12 14:23
 **/
@RestController
public class OrderServiceImpl extends BaseApiService implements IOrderService {

    @Autowired
    private MemberServiceFeign memberServiceFeign;

    @RequestMapping("/orderToMember")
    public String orderToMember() {
        UserEntity member = memberServiceFeign.getMember();
        return member.toString();
    }

    /**
     * 没有解决服务的雪崩效应
     *
     * @return
     */
    @RequestMapping("/orderToMemberUserInfo")
    public ResponseBase orderToMemberUserInfo() {
        return memberServiceFeign.getUserInfo();
    }


    /**
     * 解决服务的雪崩效应
     *
     * @return
     */
    @HystrixCommand(fallbackMethod = "orderToMemberUserInfoHystrixFallback")
    @RequestMapping("/orderToMemberUserInfoHystrix")
    public ResponseBase orderToMemberUserInfoHystrix() {
        System.out.println("线程池名称：" + Thread.currentThread().getName());
        return memberServiceFeign.getUserInfo();
    }

    public ResponseBase orderToMemberUserInfoHystrixFallback() {
        return setResultSuccess("服务降级生效了,服务器忙请稍后重试");
    }

    @RequestMapping("/orderInfo")
    public ResponseBase orderInfo() {
        System.out.println("线程池名称：" + Thread.currentThread().getName());
        return setResultSuccess();
    }

}
```

浏览器访问未使用`@HystrixCommand`注解的订单接口`localhost:8001/orderInfo`；

```
线程池名称：http-nio-8001-exec-5
```

浏览器访问使用`@HystrixCommand`注解的订单接口`localhost:8001/orderToMemberUserInfoHystrix`；

```
线程池名称：hystrix-OrderServiceImpl-2
```

`Hystrix`默认开启`10`个线程处理请求。

```
线程池名称：hystrix-OrderServiceImpl-4
线程池名称：hystrix-OrderServiceImpl-2
线程池名称：hystrix-OrderServiceImpl-3
线程池名称：hystrix-OrderServiceImpl-7
线程池名称：hystrix-OrderServiceImpl-6
线程池名称：hystrix-OrderServiceImpl-5
线程池名称：hystrix-OrderServiceImpl-8
线程池名称：hystrix-OrderServiceImpl-9
线程池名称：hystrix-OrderServiceImpl-10
线程池名称：hystrix-OrderServiceImpl-1
线程池名称：hystrix-OrderServiceImpl-1
线程池名称：hystrix-OrderServiceImpl-7
线程池名称：hystrix-OrderServiceImpl-2
线程池名称：hystrix-OrderServiceImpl-4
线程池名称：hystrix-OrderServiceImpl-3
线程池名称：hystrix-OrderServiceImpl-10
线程池名称：hystrix-OrderServiceImpl-8
线程池名称：hystrix-OrderServiceImpl-9
线程池名称：hystrix-OrderServiceImpl-6
线程池名称：hystrix-OrderServiceImpl-5
线程池名称：hystrix-OrderServiceImpl-1
线程池名称：hystrix-OrderServiceImpl-8
```

#### 验证Hystrix的服务降级

使用`Jmeter`模拟效果，线程数为`9`个时

![springcloud_hystrix_a](https://volc1612.gitee.io/blog/images/springcloud/springcloud_hystrix_a.png)

访问`orderToMemberUserInfoHystrix`接口

![springcloud_hystrix_b](https://volc1612.gitee.io/blog/images/springcloud/springcloud_hystrix_b.png)

可见当线程数为`9`个时，访问正常。这个设置线程数为`10`

![springcloud_hystrix_c](https://volc1612.gitee.io/blog/images/springcloud/springcloud_hystrix_c.png)

再次请求接口访问结果走了`orderToMemberUserInfoHystrixFallback`方法，说明`Hystrix`的线程池默认最大线程数10个，当请求数超过了最大线程数时，服务降级生效了。

![springcloud_hystrix_d](https://volc1612.gitee.io/blog/images/springcloud/springcloud_hystrix_d.png)

### 使用Hystrix定义统一Fallback接口

![springcloud_hystrix_f](https://volc1612.gitee.io/blog/images/springcloud/springcloud_hystrix_f.png)

#### 定义统一Fallback降级接口实现类

```java
import com.itmayiedu.base.BaseApiService;
import com.itmayiedu.base.ResponseBase;
import entity.UserEntity;
import itmayiedu.api.feign.MemberServiceFeign;
import org.springframework.stereotype.Component;

/**
 * @description: 会员接口feign客户端, 统一处理服务降级友好提示
 * @author: liucancan
 * @create: 2018-11-12 15:09
 **/
@Component
public class MemberServiceFeignFallback extends BaseApiService implements MemberServiceFeign {

    @Override
    public UserEntity getMember() {
        return null;
    }

    /**
     * 服务降级友好提示
     */
    @Override
    public ResponseBase getUserInfo() {
        return setResultError("第二种使用hystrix服务降级生效了。服务器忙，请稍后重试");
    }
}
```

#### 本服务接口中指定服务降级Fallback实现类

`@FeignClient(name = "app-itmayiedu-member", fallback = MemberServiceFeignFallback.class)`

```java
import com.itmayiedu.api.service.impl.IMemberService;
import itmayiedu.api.fallback.MemberServiceFeignFallback;
import org.springframework.cloud.openfeign.FeignClient;

/**
 * @description: 会员接口feign客户端
 * @author: liucancan
 * @create: 2018-11-12 15:09
 **/
@FeignClient(name = "app-itmayiedu-member", fallback = MemberServiceFeignFallback.class)
public interface MemberServiceFeign extends IMemberService {
}
```

#### 设置Hystrix超时时间

```bash
hystrix:
  command:
    default:
      execution:
#        timeout:
#          # 禁用Hystrix超时时间，生产环境不能禁用hystrix超时时间
#          enabled: false
        isolation:
          thread:
            # 设置hystrix超时时间为10秒，超过两秒后会进行服务降级
            timeoutInMilliseconds: 10000
```

#### 使用实现Fallback降级接口类

调用`orderToMemberUserInfoHystrixDemo2`方法将会走统一Fallback接口实现类`MemberServiceFeignFallback`。

```java
import com.itmayiedu.api.service.impl.IOrderService;
import com.itmayiedu.base.BaseApiService;
import com.itmayiedu.base.ResponseBase;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import entity.UserEntity;
import itmayiedu.api.feign.MemberServiceFeign;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @description: 会员接口实现类
 * @author: liucancan
 * @create: 2018-11-12 14:23
 **/
@RestController
public class OrderServiceImpl extends BaseApiService implements IOrderService {

    @Autowired
    private MemberServiceFeign memberServiceFeign;

    @RequestMapping("/orderToMember")
    public String orderToMember() {
        UserEntity member = memberServiceFeign.getMember();
        return member.toString();
    }

    /**
     * 没有解决服务的雪崩效应
     *
     * @return
     */
    @RequestMapping("/orderToMemberUserInfo")
    public ResponseBase orderToMemberUserInfo() {
        return memberServiceFeign.getUserInfo();
    }


    /**
     * 解决服务的雪崩效应
     *
     * @return
     */
    @HystrixCommand(fallbackMethod = "orderToMemberUserInfoHystrixFallback")
    @RequestMapping("/orderToMemberUserInfoHystrix")
    public ResponseBase orderToMemberUserInfoHystrix() {
        System.out.println("线程池名称：" + Thread.currentThread().getName());
        return memberServiceFeign.getUserInfo();
    }

    public ResponseBase orderToMemberUserInfoHystrixFallback() {
        return setResultSuccess("服务降级生效了,服务器忙请稍后重试");
    }

    @RequestMapping("/orderInfo")
    public ResponseBase orderInfo() {
        System.out.println("线程池名称：" + Thread.currentThread().getName());
        return setResultSuccess();
    }

    /**
     * 第二种使用hystrix方式：在service层开启独立线程池，memberServiceFeign.getUserInfo()，为
     *
     * @return
     */
    @RequestMapping("/orderToMemberUserInfoHystrixDemo2")
    public ResponseBase orderToMemberUserInfoHystrixDemo2() {
        // 这行代码为主线程
        System.out.println("线程池名称：" + Thread.currentThread().getName());
        // memberServiceFeign.getUserInfo()是在新的线程池中处理
        return memberServiceFeign.getUserInfo();
    }
}
```

![springcloud_hystrix_e](https://volc1612.gitee.io/blog/images/springcloud/springcloud_hystrix_e.png)

统一`Fallback`接口实现类生效了。

### 分布式配置中心

#### 为什么要使用分布式配置中心

如果用传统的方式管理服务配置文件，会非常复杂，如果生产环境的配置文件需要改变的时候，就要重新打`war`包重新服务配置文件到`JVM`内存中。在微服务中统一管理所有的服务配置文件信息，能够实现后台可管理，当服务器正在运行的时候如果配置文件需要改变，则不需要重启服务，能够实现更新配置信息。

#### 分布式配置中心框架

携程写的`阿波罗`，有图形化界面管理配置文件信息。配置信息放在数据库中。`SpringCloud Config`没有后台可管理配置中心，配置文件信息存放在版本控制器里面，如：`Git`、`SVN`。也可以使用`Zookeeper`实现分布式配置中心`持久节点`+`事件通知`。

#### SpringCloud Config分布式配置中心原理

##### 搭建Git环境

新建远程仓库名为`分布式Config`，新建目录名为`gkconfig`，在`gkconfig`目录中可以创建多个目录，每个目录对应一个项目的配置文件，各个目录和项目名相同，来区分不同项目的配置文件。

![springcloud_config_a](https://volc1612.gitee.io/blog/images/springcloud/springcloud_config_a.png)

#### 创建分布式config服务端

![springcloud_config_b](https://volc1612.gitee.io/blog/images/springcloud/springcloud_config_b.png)


引入依赖包

```xml
 <!--spring-cloud 整合 config-server -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
<!-- SpringBoot整合eureka客户端 -->
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.itmayiedu</groupId>
    <artifactId>springcloud-config2.0-server</artifactId>
    <version>1.0-SNAPSHOT</version>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.1.RELEASE</version>
    </parent>
    <!-- 管理依赖 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.M7</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <!--spring-cloud 整合 config-server -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
        </dependency>
        <!-- SpringBoot整合eureka客户端 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

    </dependencies>
    <!-- 注意： 这里必须要添加， 否者各种依赖有问题 -->
    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/libs-milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>
</project>
```

配置文件

```bash
# 服务注册到eureka地址，配置服务需要以别名的方式获取注册中心的服务信息，所以这里需要吧配置服务注册到注册中心中
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8100/eureka
spring:
  application:
    # 注册中心应用名称
    name: config-server
  cloud:
    config:
      server:
        git:
          # config服务读取git环境地址
          uri: https://gitee.com/volc1612/distributed_config.git
          ####搜索目录
          search-paths:
          # 表示读取远程仓库中gkconfig目录中的各服务配置文件
          - gkconfig
      # 读取分支
      label: master
# 端口号
server:
  port: 8888
```

`@EnableConfigServer`开启configserver功能

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

/**
 * @description: eureka注册中心启动类
 * @author: liucancan
 * @create: 2018-11-03 16:29
 **/
@SpringBootApplication
// 开启Eurekaserver服务注解，开启注册中心
@EnableEurekaClient
// 开启configserver功能
@EnableConfigServer
public class AppConfigServer {

    public static void main(String[] args) {
        SpringApplication.run(AppConfigServer.class, args);
    }
}
```

启动项目后，编辑配置文件

![springcloud_config_c](https://volc1612.gitee.io/blog/images/springcloud/springcloud_config_c.png)

浏览器访问

![springcloud_config_d](https://volc1612.gitee.io/blog/images/springcloud/springcloud_config_d.png)

可见分布式配置中心服务生效了，动态获取配置内容，无需重启服务。

#### 创建分布式config客户端

依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.itmayiedu</groupId>
    <artifactId>springcloud-config2.0-client</artifactId>
    <version>1.0-SNAPSHOT</version>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.1.RELEASE</version>
    </parent>
    <!-- 管理依赖 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.M7</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>

        </dependencies>
    </dependencyManagement>
    <dependencies>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-client</artifactId>
        </dependency>
        <!-- SpringBoot整合eureka客户端 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
    </dependencies>
    <!-- 注意： 这里必须要添加， 否者各种依赖有问题 -->
    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/libs-milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>
</project>
```

创建bootstrap.yml配置文件

```bash
spring:
  application:
    # 注册中心本服务名称，要和gitee中的配置文件名称一致，才能保证该服务从配合中心仓库中读取属于该服务的配置文件要和gitee中的配置文件名称一致
    name: member-server
  cloud:
    config:
      # 读取环境
      profile: sit
      # 读取config-server服务端注册时的服务id
      discovery:
        service-id: config-server
        enabled: true
# eureka服务注册地址
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8100/eureka
server:
  port: 8882
```

启动类

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

/**
 * @description: 客户端启动类,用于测试获取配置中心配置文件
 * @author: liucancan
 * @create: 2018-11-03 16:29
 **/
@SpringBootApplication
// 开启Eurekaserver服务注解，开启注册中心
@EnableEurekaClient
public class AppConfigClient {

    public static void main(String[] args) {
        SpringApplication.run(AppConfigClient.class, args);
    }
}
```

测试controller

```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @description: 客户端获取分布式配置中心配置文件
 * @author: liucancan
 * @create: 2018-11-20 16:28
 **/
@RestController
public class ConfigController {


    @Value("${test}")
    private String test;
    @RequestMapping("/getConfig")
    public String getConfig(){
        return test;
    }
}
```

![springcloud_config_e](https://volc1612.gitee.io/blog/images/springcloud/springcloud_config_e.png)

#### 配置文件实时刷新

`springcloud config`默认不是实时刷新的，可以使用手动刷新(人工调用接口读读取配置中心文件)和自动刷新(消息总线spring bus)，都可实现实时获取配置文件，无需重启服务。在公司中不建议使用自动刷新，因为自动刷新增加服务压力对服务性能不是很好，建议使用手动刷新配置文件。

##### 手动刷新配置文件

添加依赖

```xml
<!-- actuator监控中心 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

配置文件开启监控断点

```bash
management:
  endpoints:
    web:
      exposure:
        # 开启监控所有端点
        include: "*"
```

```bash
spring:
  application:
    # 注册中心本服务名称，要和gitee中的配置文件名称一致，才能保证该服务从配合中心仓库中读取属于该服务的配置文件
    name: member-server
  cloud:
    config:
      # 读取后缀
      profile: sit
      # 读取config-server注册地址
      discovery:
        service-id: config-server
        enabled: true
# eureka服务注册地址
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8100/eureka
server:
  port: 8882
management:
  endpoints:
    web:
      exposure:
        # 开启监控所有端点
        include: "*"
```

在需要刷新的`Bean`上添加`@RefreshScope`注解

```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.stereotype.Component;

/**
 * 获取配置文件类
 */
@Component
@RefreshScope
public class ConfigService {
    @Value("${test}")
    private String test;

    public String getTest() {
        return test;
    }

    public void setTest(String test) {
        this.test = test;
    }
}
```

使用配文件接口

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @description: 客户端获取分布式配置中心配置文件
 * @author: liucancan
 * @create: 2018-11-20 16:28
 */
@RestController
public class ConfigController {
    @Autowired
    private ConfigService config;

    @RequestMapping("/getConfig")
    public String getConfig() {
        return config.getTest();
    }
}
```

要想获取到新的配置文件内容，需要向该服务发送POST请求手动更新，`http://localhost:8882/actuator/refresh`。

![springcloud_config_f](https://volc1612.gitee.io/blog/images/springcloud/springcloud_config_f.png)

![springcloud_config_g](https://volc1612.gitee.io/blog/images/springcloud/springcloud_config_g.png)

该客户端的配置文件更新了。

* 配置文件获取时出现乱码问题

解决方法上是重新以UTF-8编码编写配置文件上传到配文文件远程仓库中。

### 如何实现一套API接口

可从以下方面回答：接口权限(开放接口和内部接口，内部接口即局域网接口，需要使用网关`Gateway`网络连接，实现内外网互通。)、考虑幂等性(重复提交)、安全性(`https`)、签名(防止篡改数据)、使用网关实现黑白名单、接口遵循Restful风格(跨平台)、高并发(服务保护，服务的隔离，熔断，降级)、使用统一`API`管理平台(`swagger`)。

### 网关

网关： 相当于所有请求先经过网关你服务器上，允许请求后再由网关服务器转发请求到实际的服务器上，类似`nginx`。

功能： 可以拦截器所有请求，对请求做权限权限控制、负载均衡、日志管理、接口调用监控等。

#### Zull和Nginx

相同点： 都可实现负载均衡，反向代理，过滤请求。
不同点： `Nginx`使用`C`语言，`Zuul`使用`Java`编写；`Zull`依赖`Ribbon`和`Eureka`实现本地负载均衡转发;同时`Nginx`可以更好的抗高并发，`Zuul`网关适用于请求过滤和拦截等；`Nginx`使用服务器端负载均衡的；`Nginx`实现反向代理；`Zuul`实现微服务网关拦截；

#### 搭建Zuul网关

##### 创建网关项目

引入jar包

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
</dependency>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.itmayiedu</groupId>
    <artifactId>springcloud-2.0-zuul-gateway</artifactId>
    <version>1.0-SNAPSHOT</version>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.1.RELEASE</version>
    </parent>
    <!-- 管理依赖 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.M7</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
        </dependency>
        <!-- SpringBoot整合eureka客户端 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
    </dependencies>
    <!-- 注意： 这里必须要添加， 否者各种依赖有问题 -->
    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/libs-milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>
</project>
```

配置文件

```bash
# 注册中心
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8100/eureka/
server:
  # 网关服务端口号
  port: 8088
spring:
  application:
    # 网关名称
    name: service-zuul
# 配置网关反向代理
zuul:
  routes:
    # api-a可以修改为对应服务别名，如订单服务别名(app-itmayiedu-order)
    api-a:
      # 以 /api-member/访问转发到会员服务
      path: /api-member/**
      # 配置要代理的会员服务别名id，zuul默认整合了Ribbon实现了本地负载均衡，当访问http://127.0.0.1:80/api-member时会访问到会员项目，实现本地转发
      serviceId: app-itmayiedu-member
    api-b:
      # 以 /api-order/访问转发到订单服务
      path: /api-order/**
      # 配置要代理的订单服务别名id
      serviceId: app-itmayiedu-order
```

网关项目启动类

`@EnableZuulProxy`开启网关反向代理功能

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

/**
 * @description: 订单服务启动类
 * @author: liucancan
 * @create: 2018-11-03 16:29
 **/
@SpringBootApplication
// 将该服务注册到eureka注册中心
@EnableEurekaClient
// 服务开启Zuul网关反向代理功能
@EnableZuulProxy
public class AppZuulGateWay {
    public static void main(String[] args) {
        SpringApplication.run(AppZuulGateWay.class, args);
    }
}
```

会员项目资源接口

```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @description: 会员服务接口
 * @author: liucancan
 * @create: 2018-11-03 17:54
 **/
@RestController
public class MemberController {

    @Value("${server.port}")
    private int port;

    @RequestMapping("/getMember")
    public String getMember() {
        return "springcloud2.0会员服务" + port;
    }

    @RequestMapping("/")
    public String showMember() {
        return "我是会员服务";
    }
}
```

浏览器访问网关查看反向代理功能是否生效。

![springcloud_zuul_a](https://volc1612.gitee.io/blog/images/springcloud/springcloud_zuul_a.png)

#### zuul网关过滤器的使用

功能验证token。

创建zuul过滤器

```java
import com.netflix.zuul.ZuulFilter;
import com.netflix.zuul.context.RequestContext;
import com.netflix.zuul.exception.ZuulException;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * @description: zuul过滤器拦截token
 * @author: liucancan
 * @create: 2018-11-26 12:45
 **/
@Component
public class TokenFilter extends ZuulFilter {

    @Value("${server.port}")
    private String serverPort;

    /**
     * 定义过滤器是否生效
     *
     * @return
     */
    @Override
    public boolean shouldFilter() {
        return true;
    }

    /**
     * 编写拦截器业务逻辑实现代码
     *
     * @return
     * @throws ZuulException
     */
    @Override
    public Object run() throws ZuulException {
        // 当前上下文对象
        RequestContext context = RequestContext.getCurrentContext();
        // 获取request对象
        HttpServletRequest request = context.getRequest();
        HttpServletResponse response = context.getResponse();
        response.setContentType("text/html;charset=UTF-8");
        String userToken = request.getParameter("userToken");
        // 返回错误提示
        if (null == userToken) {
            // 停止请求继续执行
            context.setSendZuulResponse(false);
            context.setResponseBody("userToken is null 网关项目端口号：" + serverPort);
            context.setResponseStatusCode(401);
            return null;
        }
        return null;
    }

    /**
     * 定义过滤类型，如 per : 标识请求执行前执行
     *
     * @return
     */
    @Override
    public String filterType() {
        return "pre";
    }

    /**
     * 定义该过滤器在多个过滤器中的执行顺序
     *
     * @return
     */
    @Override
    public int filterOrder() {
        return 0;
    }
}
```

![springcloud_zuul_b](https://volc1612.gitee.io/blog/images/springcloud/springcloud_zuul_b.png)

网关`Zuul`过滤器生效了。

#### 网关Zuul负载均衡

`Zuul`默认开启`Ribbon`本地负载均衡。

![springcloud_zuul_c](https://volc1612.gitee.io/blog/images/springcloud/springcloud_zuul_c.gif)

#### 搭建动态网关Zuul路由转发

动态网关项目需要从配置服务中心`springcloud configserver`中获取远程仓库中的`zuul`配置内容。

引入依赖

```xml
<!-- actuator监控中心 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<!-- springcloud config 2.0 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```        

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.itmayiedu</groupId>
    <artifactId>springcloud-2.0-zuul-gateway</artifactId>
    <version>1.0-SNAPSHOT</version>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.1.RELEASE</version>
    </parent>
    <!-- 管理依赖 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.M7</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
        </dependency>
        <!-- SpringBoot整合eureka客户端 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

        <!-- actuator监控中心 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!-- springcloud config 2.0 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-client</artifactId>
        </dependency>
    </dependencies>
    <!-- 注意： 这里必须要添加， 否者各种依赖有问题 -->
    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/libs-milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>
</project>
```

配置文件

```bash
# 注册中心
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8100/eureka/
  instance:
    ### Eureka客户端向服务端发送心跳的时间间隔，单位为秒（客户端告诉服务端自己会按照该规则，每隔1秒发送一次心跳包
    lease-renewal-interval-in-seconds: 1
    #### Eureka服务端在收到最后一次心跳之后等待的时间上限，单位为秒，超过则剔除（客户端告诉服务端按照此规则等待自己）
    lease-expiration-duration-in-seconds: 2
server:
  # 网关服务端口号
  port: 8088
spring:
  application:
    # 网关名称
    name: service-zuul
  cloud:
    config:
      # 读取后缀
      profile: dev
      # 读取config-server注册地址
      discovery:
        service-id: config-server
        enabled: true
# zuul配置文件放到了配置中心远程仓库中
## 配置网关反向代理
#zuul:
#  routes:
#    # api-a可以修改为对应服务别名，如订单服务别名(app-itmayiedu-order)
#    api-a:
#      # 以 /api-member/访问转发到会员服务
#      path: /api-member/**
#      # 配置要代理的会员服务别名id，zuul默认整合了Ribbon实现了本地负载均衡，当访问http://127.0.0.1:80/api-member时会访问到会员项目，实现本地转发
#      serviceId: app-itmayiedu-member
#    api-b:
#      # 以 /api-order/访问转发到订单服务
#      path: /api-order/**
#      # 配置要代理的订单服务别名id
#      serviceId: app-itmayiedu-order

# 开启所有监控中心接口
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

启动类

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;
import org.springframework.cloud.netflix.zuul.filters.ZuulProperties;

/**
 * @description: 订单服务启动类
 * @author: liucancan
 * @create: 2018-11-03 16:29
 **/
@SpringBootApplication
// 将该服务注册到eureka注册中心
@EnableEurekaClient
// 服务开启Zuul网关反向代理功能
@EnableZuulProxy
public class AppZuulGateWay {
    public static void main(String[] args) {
        SpringApplication.run(AppZuulGateWay.class, args);
    }

    /**
     * zuul配置能够使用config实现实时更新，手动调用此接口可以让本服务从远程配置仓库中更新zuul配置信息
     */
    @RefreshScope
    @ConfigurationProperties("zuul")
    public ZuulProperties zuulProperties() {
        return new ZuulProperties();
    }
}
```

远程配置文件

![springcloud_zuul_c](https://volc1612.gitee.io/blog/images/springcloud/springcloud_zuul_c.png)

内容如下：

```bash
# 配置网关反向代理
zuul:
  routes:
    # api-a可以修改为对应服务别名，如订单服务别名(app-itmayiedu-order)
    api-a:
      # 以 /api-member/访问转发到会员服务
      path: /api-member/**
      # 配置要代理的会员服务别名id，zuul默认整合了Ribbon实现了本地负载均衡，当访问http://127.0.0.1:80/api-member时会访问到会员项目，实现本地转发
      serviceId: app-itmayiedu-member
    api-b:
      # 以 /api-order/访问转发到订单服务
      path: /api-order/**
      # 配置要代理的订单服务别名id
      serviceId: app-itmayiedu-order
```

启动网关项目，启动配置中心服务。

网关项目手动更新`zuul`网关配置信息

![springcloud_zuul_d](https://volc1612.gitee.io/blog/images/springcloud/springcloud_zuul_d.png)

浏览器访问

![springcloud_zuul_e](https://volc1612.gitee.io/blog/images/springcloud/springcloud_zuul_e.png)

上图说明网关服务通过配置中心服务获取到了`zuul配置信息`。

***测试动态网关配置是否生效***

动态修改`service-zuul-dev.yml`配置文件

```bash
# 配置网关反向代理
zuul:
  routes:
    # api-a可以修改为对应服务别名，如订单服务别名(app-itmayiedu-order)
    api-a:
      # 以 /api-member/访问转发到会员服务
      path: /api-member/**
      # 配置要代理的会员服务别名id，zuul默认整合了Ribbon实现了本地负载均衡，当访问http://127.0.0.1:80/api-member时会访问到会员项目，实现本地转发
      serviceId: app-itmayiedu-member
    api-b:
      # 以 /api-order/访问转发到订单服务
      path: /api-order/**
      # 配置要代理的订单服务别名id
      serviceId: app-itmayiedu-order
    api-c:
      # 以 /api-pay/访问转发到支付服务
      path: /api-pay/**
      # 配置要代理的支付服务别名id
      serviceId: app-itmayiedu-pay
```

手动更新`zuul`配置

![springcloud_zuul_d](https://volc1612.gitee.io/blog/images/springcloud/springcloud_zuul_d.png)

访问

![springcloud_zuul_f](https://volc1612.gitee.io/blog/images/springcloud/springcloud_zuul_f.png)

### 网关集群

网关`Zuul`实现集群。

`Nginx`配文件

```bash
worker_processes  1;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    # 配置上游服务集群，默认使用轮训机制
    upstream  backServer{
        server 127.0.0.1:81;
        server 127.0.0.1:82;
    }
    server {
        listen       80;
        server_name  wg.itmayiedu.com;
        location / {
            ### 指定上游服务器负载均衡服务器
            proxy_pass http://backServer/;
            index  index.html index.htm;
        }
    }
}
```

启动`Nginx`。

配置hosts文件

![springcloud_zuul_g](https://volc1612.gitee.io/blog/images/springcloud/springcloud_zuul_g.png)

演示

![springcloud_zuul_g](https://volc1612.gitee.io/blog/images/springcloud/springcloud_zuul_g.gif)

#### Nginx和Zuul区别

微服务网关是针对整个微服务同一实现请求拦截的。使用熟悉的语言整合功能，便于学习。`Nginx`针对所有服务的。

* 网关对所有会话进行拦截
* 网关安全控制，同一异常，xxs，sql注入;
* 权限控制，黑白名单，性能监控，日志打印;

### Swagger接口管理平台

![springcloud_swagger_a](https://volc1612.gitee.io/blog/images/springcloud/springcloud_swagger_a.png)

单独使用`Swagger`需要引入两个依赖包，和添加配置文件。

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.8.0</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.8.0</version>
</dependency>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.itmayiedu</groupId>
    <artifactId>springcloud-swagger2.0</artifactId>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.1.RELEASE</version>
    </parent>
    <!-- 管理依赖 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.M7</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- SpringBoot整合eureka客户端 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!-- swagger2 -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.8.0</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.8.0</version>
        </dependency>
    </dependencies>
    <!-- 注意： 这里必须要添加， 否者各种依赖有问题 -->
    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/libs-milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>
</project>
```

配置文件

```bash
# 服务注册到eureka地址，配置服务需要以别名的方式获取注册中心的服务信息，所以这里需要吧配置服务注册到注册中心中
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8100/eureka
  instance:
    ### Eureka客户端向服务端发送心跳的时间间隔，单位为秒（客户端告诉服务端自己会按照该规则，每隔1秒发送一次心跳包
    lease-renewal-interval-in-seconds: 1
    #### Eureka服务端在收到最后一次心跳之后等待的时间上限，单位为秒，超过则剔除（客户端告诉服务端按照此规则等待自己）
    lease-expiration-duration-in-seconds: 2
spring:
  application:
    # 注册中心应用名称
    name: swagger2.0-server
# 端口号
server:
  port: 9999
```  

配置类，`@EnableSwagger2`开启`swagger`

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

/**
 * @param
 * @author liucancan
 * @description @EnableSwagger2 [项目开启Swagger2功能]
 * @return
 * @date 2018/11/27
 */
@Configuration
@EnableSwagger2
public class SwaggerConfig {

    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2).apiInfo(apiInfo()).select()
                // api扫包
                .apis(RequestHandlerSelectors.basePackage("com.itmayiedu.api")).paths(PathSelectors.any()).build();
    }

    /**
     * 配置文档基础信息
     *
     * @param
     * @return springfox.documentation.service.ApiInfo
     * @author liucancan
     * @date 15:45 2018/11/27
     */
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder().title("每特教育|蚂蚁课堂 微服务电商系统").description("每特教育|蚂蚁课堂 Java分布式&微服务培训")
                .termsOfServiceUrl("http://www.itmayiedu.com")
                // .contact(contact)
                .version("1.0").build();
    }
}
```

测试接口类

```java
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiImplicitParam;
import io.swagger.annotations.ApiOperation;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @description: 演示swagger
 * @author: liucancan
 * @create: 2018-11-27 15:46
 */
@Api("控制器功能名称-swagger演示")
@RestController
public class SwaggerController {

    @ApiOperation("接口名称")
    @GetMapping("/swaggerIndex")
    public String getSwagger() {
        return "测试";
    }

    @ApiOperation("带参数接口")
    @ApiImplicitParam(name = "userName", value = "用户信息名称", required = true, type = "String")
    @GetMapping("/paramSwaggerIndex")
    public String paramSwaggerIndex(String userName) {
        return userName;
    }
}
```

启动类

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @description: eureka注册中心启动类
 * @author: liucancan
 * @create: 2018-11-03 16:29
 **/
@SpringBootApplication
public class AppSwagger {
    public static void main(String[] args) {
        SpringApplication.run(AppSwagger.class, args);
    }
}
```

浏览器访问： `http://localhost:9999/swagger-ui.html`

![springcloud_swagger_b](https://volc1612.gitee.io/blog/images/springcloud/springcloud_swagger_b.png)

#### Swagger集群

管理整个微服务接口文档，两种方式：
* 使用网关`Zuul+Swagger`。
* 使用`Nginx+Swagger`，以项目不同区分转发到不同的接口文档服务;

这里演示下使用网关`Zuul+Swagger`整合微服务`swagger`接口管理平台。`SpringBoot`项目中支持`Swagger`接口的管理，微服务项目中只需要引入一个整合`Swagger`依赖包。这里使用会员项目和订单项目做`Swagger`整合演示，以会员项目为例。

引入依赖

```xml
<dependency>
    <groupId>com.spring4all</groupId>
    <artifactId>swagger-spring-boot-starter</artifactId>
    <version>1.7.0.RELEASE</version>
</dependency>
```

项目配置文件，指定扫描接口包

```bash
swagger:
  # 指定包下的接口会生成接口文档
  base-package: com.itmayiedu.api
```

```bash
### 服务启动端口号
server:
  port: 8113
### 服务名称(服务注册到eureka名称)，服务注册到哦注册中心的服务名称
spring:
  application:
    name: app-itmayiedu-member
### 服务注册到eureka地址
eureka:
  client:
    service-url:
      # 当前服务注册到eureka服务的地址
      defaultZone: http://localhost:8100/eureka
    ### 注册到注册中心
    register-with-eureka: true
    ### 是否需要从eureka上获取注册信息，检索服务
    fetch-registry: true
  instance:
    ### Eureka客户端向服务端发送心跳的时间间隔，单位为秒（客户端告诉服务端自己会按照该规则，每隔1秒发送一次心跳包
    lease-renewal-interval-in-seconds: 1
    #### Eureka服务端在收到最后一次心跳之后等待的时间上限，单位为秒，超过则剔除（客户端告诉服务端按照此规则等待自己）
    lease-expiration-duration-in-seconds: 2
swagger:
  # 指定包下的接口会生成接口文档
  base-package: com.itmayiedu.api
```

项目启动类，`@EnableSwagger2Doc`项目开启`swagger`功能

```java
import com.spring4all.swagger.EnableSwagger2Doc;
import io.swagger.annotations.Api;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

/**
 * @description: eureka测试启动类
 * @EnableEurekaClient [将该服务注册到eureka注册中心]
 * @EnableSwagger2Doc [服务开启swagger接口管理平台，无需编写swagger配置文件类]
 * @author: liucancan
 * @create: 2018-11-03 16:29
 */
@SpringBootApplication
@EnableEurekaClient
@EnableSwagger2Doc
@Api("会员项目接口平台")
public class AppMember {
    public static void main(String[] args) {
        SpringApplication.run(AppMember.class, args);
    }
}
```

订单项目也是一样

网关项目也需要引入依赖，并把过滤器关掉

```xml
<dependency>
    <groupId>com.spring4all</groupId>
    <artifactId>swagger-spring-boot-starter</artifactId>
    <version>1.7.0.RELEASE</version>
</dependency>
```

网关项目启动类，`@EnableSwagger2Doc`开启`swagger`功能，和编写整个微服务`swagger`组件

```java
import com.spring4all.swagger.EnableSwagger2Doc;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;
import org.springframework.cloud.netflix.zuul.filters.ZuulProperties;
import org.springframework.context.annotation.Primary;
import org.springframework.stereotype.Component;
import springfox.documentation.swagger.web.SwaggerResource;
import springfox.documentation.swagger.web.SwaggerResourcesProvider;

import java.util.ArrayList;
import java.util.List;

/**
 * @description: 订单服务启动类
 * @EnableEurekaClient [将该服务注册到eureka注册中心]
 * @EnableZuulProxy [服务开启Zuul网关反向代理功能]
 * @EnableSwagger2Doc [服务开启swagger接口管理平台，无需编写swagger配置文件类]
 * @author: liucancan
 * @create: 2018-11-03 16:29
 **/
@SpringBootApplication
@EnableEurekaClient
@EnableZuulProxy
@EnableSwagger2Doc
public class AppZuulGateWay {
    public static void main(String[] args) {
        SpringApplication.run(AppZuulGateWay.class, args);
    }

    /**
     * zuul配置能够使用config实现实时更新，手动调用此接口可以让本服务从远程配置仓库中更新zuul配置信息
     */
    @RefreshScope
    @ConfigurationProperties("zuul")
    public ZuulProperties zuulProperties() {
        return new ZuulProperties();
    }

    /**
     * 编写网关项目整合所有微服务swagger组件
     *
     * @param
     * @author liucancan
     * @date 17:45 2018/11/27
     * @return
     */
    @Component
    @Primary
    class DocumentationConfig implements SwaggerResourcesProvider {
        @Override
        public List<SwaggerResource> get() {
            List resources = new ArrayList<>();
            /**
             * swaggerResource("自定义服务swagger名称","/网关项目zuul配置中的对应服务path值/v2/api-docs","该服务swagger接口版本号"
             */
            resources.add(swaggerResource("app-itmayiedu-member", "/api-member/v2/api-docs", "2.0"));
            resources.add(swaggerResource("app-itmayiedu-order", "/api-order/v2/api-docs", "2.0"));
            return resources;
        }

        private SwaggerResource swaggerResource(String name, String location, String version) {
            SwaggerResource swaggerResource = new SwaggerResource();
            swaggerResource.setName(name);
            swaggerResource.setLocation(location);
            swaggerResource.setSwaggerVersion(version);
            return swaggerResource;
        }
    }
}
```

启动注册中心服务、会员服务、订单服务、配置中心服务、网关服务，浏览器访问查看效果

![springcloud_swagger_c](https://volc1612.gitee.io/blog/images/springcloud/springcloud_swagger_c.png)







[1]: http://www.cnblogs.com/xishuai/p/spring-cloud-eureka-safe.html
[2]: https://springcloud.cc/spring-cloud-dalston.html