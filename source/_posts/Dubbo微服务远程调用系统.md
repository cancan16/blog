---
title: Dubbo微服务远程调用系统
date: 2019-10-05 21:11:32
update: 2019-10-05 21:11:32
categories: DUBBO
tags: [dubbo]
---

### Dubbo Provider(服务方)的搭建

####  引入pom依赖

```xml
<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo</artifactId>
    <version>2.7.2</version>
    <exclusions>
        <exclusion>
            <artifactId>spring</artifactId>
            <groupId>org.springframework</groupId>
        </exclusion>
    </exclusions>
</dependency>
<!-- https://mvnrepository.com/artifact/org.apache.dubbo/dubbo -->

<dependency>
    <groupId>org.apache.zookeeper</groupId>
    <artifactId>zookeeper</artifactId>
    <version>3.4.10</version>
    <exclusions>
        <exclusion>
            <artifactId>slf4j-log4j12</artifactId>
            <groupId>org.slf4j</groupId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>com.github.sgroschupf</groupId>
    <artifactId>zkclient</artifactId>
    <version>0.1</version>
</dependency>
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-recipes</artifactId>
    <version>4.0.1</version>
</dependency>
```

#### 修改配置文件

```yml
# dubbo config
# 应用定义了提供方应用信息，用于计算依赖关系；在 dubbo-admin 或 dubbo-monitor 会显示这个名字，方便辨识
dubbo:
  application:
    name: user-app
  # 使用 zookeeper 注册中心暴露服务，注意要先开启 zookeeper
  # 注册中心id
  registry:
    id: zookeeper-registry
    # 注册中心协议
    protocol: zookeeper
    # 注册中心地址
    address: 127.0.0.1:2181

  # dubbo协议在20880端口暴露服务
  # 协议名称
  protocol:
    name: dubbo
    # 协议端口
    port: 20880
    # 协议访问log
    accesslog: dubbo-access.log
  # 重试次数
  provider:
    retries: 0
    # 超时时间
    timeout: 3000
  # 注册监控中心
  monitor:
    protocol: registry
```

#### 开启注解

```java
import org.apache.dubbo.config.spring.context.annotation.DubboComponentScan;
import org.apache.dubbo.config.spring.context.annotation.EnableDubboConfig;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@EnableDubboConfig
// com.xdclass.userapp.service.dubbo 服务项目的接口实现类所在包路径
@DubboComponentScan("com.xdclass.userapp.service.dubbo")
@MapperScan("com.xdclass.userapp.mapper")
public class UserAppApplication {
    public static void main(String[] args) {
        SpringApplication.run(UserAppApplication.class, args);
    }
}
```

#### 服务项目接口实现类

```java
package com.xdclass.userapp.service.dubbo;

import com.xdclass.userapi.VO.UserVO;
import com.xdclass.userapi.service.IUserService;
import com.xdclass.userapp.domain.TUser;
import com.xdclass.userapp.mapper.TUserMapper;
import org.apache.dubbo.config.annotation.Service;
import org.springframework.beans.BeanUtils;

import javax.annotation.Resource;

@Service
public class UserService implements IUserService {
    @Resource
    private TUserMapper userMapper;

    /**
     * 通过用户id获取用户信息接口
     *
     * @param id
     * @return
     */
    @Override
    public UserVO getUserById(int id) {
        TUser tUser = userMapper.selectByPrimaryKey(id);
        UserVO userVO = new UserVO();
        BeanUtils.copyProperties(tUser, userVO);
        return userVO;
    }
}
```

* 进行@Service注解扫描  @org.apache.dubbo.config.annotation.Service
* 序列化传输对象   
* 对比加入dubbo依赖和没加依赖前后的区别   
  * dubbo跑起来了
  * ZK连接上了
  * dubbo端口和http端口同时跑起来了


### Dubbo Comsumer服务调用方的配置以及注意项

#### 引入服务提供者的接口项目(`user-service-api`的jar包)

`<version></version>`引用的提供服务项目的接口jar包报错，不用管，项目仍然可以正常启动。

#### 引入pom依赖

```xml
<!-- 新增 dubbo 依赖 -->
<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo</artifactId>
    <version>2.7.2</version>
    <exclusions>
        <exclusion>
            <artifactId>spring</artifactId>
            <groupId>org.springframework</groupId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.apache.zookeeper</groupId>
    <artifactId>zookeeper</artifactId>
    <version>3.4.10</version>
    <exclusions>
        <exclusion>
            <artifactId>slf4j-log4j12</artifactId>
            <groupId>org.slf4j</groupId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>com.github.sgroschupf</groupId>
    <artifactId>zkclient</artifactId>
    <version>0.1</version>
</dependency>
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-recipes</artifactId>
    <version>4.0.1</version>
</dependency>
```

#### 修改配置文件

```yml
dubbo:
  application:
    name: user-coupon
  # 使用 zookeeper 注册中心暴露服务，注意要先开启 zookeeper
  # 注册中心id
  registry:
    id: zookeeper-registry
    # 注册中心协议
    protocol: zookeeper
    # 注册中心地址
    address: 127.0.0.1:2181

  # dubbo协议在20880端口暴露服务
  # 协议名称
  protocol:
    name: dubbo
    # 协议端口
    port: 20881
    # 协议访问log
    accesslog: dubbo-access.log
  # 重试次数
  provider:
    retries: 0
    # 超时时间
    timeout: 3000
  # 注册监控中心
  monitor:
    protocol: registry
```

#### 开启注解

```java
import org.apache.dubbo.config.spring.context.annotation.DubboComponentScan;
import org.apache.dubbo.config.spring.context.annotation.EnableDubboConfig;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@EnableDubboConfig
// 提供服务项目的接口实现类包路径(提供服务的实例已经交给了Dubbo管理，这里只需要引用路径即可)
@DubboComponentScan("com.xdclass.userapp.service.dubbo")
@MapperScan("com.xdclass.couponapp.mapper")
public class CouponAppApplication {
    public static void main(String[] args) {
        SpringApplication.run(CouponAppApplication.class, args);
    }
}

```

#### 在调用的地方加入注解@Reference

```java
package com.xdclass.couponapp.service;

import com.xdclass.couponapp.domain.TCoupon;
import com.xdclass.couponapp.domain.TCouponExample;
import com.xdclass.couponapp.mapper.TCouponMapper;
import com.xdclass.userapi.service.IUserService;
import org.apache.dubbo.config.annotation.Reference;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;
import java.util.List;

@Service
public class CouponService {

    @Resource
    private TCouponMapper tCouponMapper;

    /***引用提供服务的接口对象*/
    @Reference
    private IUserService iUserService;

    public void print() {
        System.err.println("enter coupon service");
    }

    public String query() {
        TCouponExample example = new TCouponExample();
        example.createCriteria().andCodeEqualTo("0057da3c-f2ad-42bd-b6d2-8bb58b6dbc90");
        List<TCoupon> tCoupon = tCouponMapper.selectByExample(example);
        return tCoupon.get(0).toString();
    }

    public String getUserById(int id) {
        return iUserService.getUserById(id).toString();
    }
}
```