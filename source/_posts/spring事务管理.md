---
title: spring事务管理
date: 2018-04-22 16:19:27
update: 2018-04-22 16:19:27
categories: TRANSACTION
tags: [transaction]
---

### spring事务管理

AOP(Aspect Oriented Programming)面向切面编程，在项目中可以使用AOP进行生成日志，数据库读写分离，数据回滚等。

Spring配置文件中关于事务配置总是由三个组成部分，分别是DataSource、TransactionManager和代理机制这三部分，无论哪种配置方式，一般变化的只是代理机制这部分。

<!-- more -->

#### 注解式事务

在servlet-context.xml配置文件中加入注解式事务声明

```
<!-- 使用JDBC事物 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"/>
</bean>
<!--使用注解事务 -->
<tx:annotation-driven/>
```

servlet-context.xml配置

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop" xmlns:task="http://www.springframework.org/schema/task"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
	   http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd
       http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.2.xsd http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- 使用JDBC事物 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--使用注释事务 -->
    <tx:annotation-driven/>

    <!-- 扫描 controller -->
    <context:component-scan base-package="com.cn.nullframe"/>

    <!-- 定义试图解析器 -->
    <!-- Example: prefix="/WEB-INF/jsp/", suffix=".jsp", viewname="test" ->
        "/WEB-INF/jsp/test.jsp" -->
    <bean
            class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/view/"/>
        <property name="suffix" value=".html"/>
    </bean>

    <mvc:annotation-driven>
        <!-- 编码转换 -->
        <mvc:message-converters>
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <property name="supportedMediaTypes">
                    <list>
                        <value>text/plain;charset=UTF-8</value>
                        <value>text/html;charset=UTF-8</value>
                        <value>application/json;charset=UTF-8</value>
                    </list>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
</beans>
```
#### 使用

在UserService类上添加@Transactional

```
import com.cn.nullframe.mapper.base.UserMapper;
import com.cn.nullframe.pojo.User;
import com.cn.nullframe.service.base.BaseService;
import com.github.abel533.entity.Example;
import com.github.pagehelper.PageHelper;
import com.github.pagehelper.PageInfo;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import java.util.List;

@Service
@Transactional
public class UserService extends BaseService<User> {

    @Autowired
    private UserMapper userMapper;


    /**
     * 测试分页插件
     *
     * @param page
     * @param rows
     * @return
     */
    public PageInfo<User> queryPageList(String name, Integer page, Integer rows) {
        Example example = new Example(User.class);
        Example.Criteria criteria = example.createCriteria();
        criteria.andEqualTo("name", name);
        example.setOrderByClause("updated DESC");
        // 设置分页参数 一定要放在查询前面
        /**
         * 最终执行的sql:
         SELECT ID,NAME,MOBILE,PHONE,PASSWORD,EMAIL,CREATED,UPDATED FROM user WHERE ( NAME = ? ) order by updated DESC LIMIT ?
         */
        PageHelper.startPage(page, rows);
        List<User> list = userMapper.selectByExample(example);
        return new PageInfo<>(list);
    }

    /**
     * 测试事物是否生效
     */
    public String testTtransaction() {
        User user = new User();
        user.setName("111111");
        user.setId(1l);
        userMapper.updateByPrimaryKey(user);
        int i = 1 / 0;
        return "123";
    }
}

```

日志查看说明已回滚

```
2018-04-22 21:14:18 DEBUG com.cn.nullframe.mapper.UserMapper.insert!selectKey:145 - ==>  Executing: SELECT LAST_INSERT_ID() 
2018-04-22 21:14:18 DEBUG com.cn.nullframe.mapper.UserMapper.insert!selectKey:145 - <==      Total: 1
2018-04-22 21:14:18 DEBUG org.mybatis.spring.SqlSessionUtils:188 - Releasing transactional SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@2a1dd133]
2018-04-22 21:14:18 DEBUG org.mybatis.spring.SqlSessionUtils:312 - Transaction synchronization deregistering SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@2a1dd133]
2018-04-22 21:14:18 DEBUG org.mybatis.spring.SqlSessionUtils:317 - Transaction synchronization closing SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@2a1dd133]
2018-04-22 21:14:18 DEBUG org.springframework.jdbc.datasource.DataSourceTransactionManager:847 - Initiating transaction rollback
2018-04-22 21:14:18 DEBUG org.springframework.jdbc.datasource.DataSourceTransactionManager:284 - Rolling back JDBC transaction on Connection [ConnectionHandle{url=jdbc:mysql://localhost:3306/nullframe?useUnicode=true&characterEncoding=utf8&autoReconnect=true&allowMultiQueries=true, user=root, debugHandle=null, lastResetAgoInSec=0, lastUsedAgoInSec=0, creationTimeAgoInSec=0}]
2018-04-22 21:14:18 DEBUG org.springframework.jdbc.datasource.DataSourceTransactionManager:327 - Releasing JDBC Connection [ConnectionHandle{url=jdbc:mysql://localhost:3306/nullframe?useUnicode=true&characterEncoding=utf8&autoReconnect=true&allowMultiQueries=true, user=root, debugHandle=null, lastResetAgoInSec=0, lastUsedAgoInSec=0, creationTimeAgoInSec=0}] after transaction
2018-04-22 21:14:18 DEBUG org.springframework.jdbc.datasource.DataSourceUtils:327 - Returning JDBC Connection to DataSource
```

@Transactional注解说明：

（1）注解类上，这样你配置的这个@Transactional 对这个类中的所有public方法都起作用

（2）注解方法上，只对这个方法有作用，同样必须是public的方法

### 声明式事物

事务配置文件app-transaction.xml
需要注意的是：

在其他的Spring配置文件中应该去掉扫描com.cn.nullframe.service层，因为app-transaction.xml中已经扫描过了

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop" xmlns:task="http://www.springframework.org/schema/task"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
	   http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd
       http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.2.xsd http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task.xsd">

    <!-- 扫描 controller -->
    <context:component-scan base-package="com.cn.nullframe.service"/>

    <!-- 定义事务管理器 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!-- 配置事务通知策略 -->
    <tx:advice id="transactionAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="select*" read-only="true" propagation="REQUIRED"/>
            <tx:method name="delete*" propagation="REQUIRED" rollback-for="Exception"/>
            <tx:method name="update*" propagation="REQUIRED" rollback-for="Exception"/>
            <tx:method name="insert*" propagation="REQUIRED" rollback-for="Exception"/>
            <tx:method name="*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>

    <!-- 配置AOP切面 -->
    <aop:config>
        <aop:pointcut id="transactionPointcut" expression="execution(* com.cn.nullframe.service..*.*(..))"/>
        <aop:advisor pointcut-ref="transactionPointcut" advice-ref="transactionAdvice"/>
    </aop:config>

</beans>
```
### 事务回滚和异常关系

#### 异常的一些基本知识
##### 异常的架构

异常的继承结构：Throwable为基类，Error和Exception继承Throwable。Error和RuntimeException及其子类成为未检查异常（unchecked），其它异常成为已检查异常（checked）

![a](https://volc1612.gitee.io/blog/images/transaction/a.jpg)

##### Error异常

Error表示程序在运行期间出现了十分严重、不可恢复的错误，在这种情况下应用程序只能中止运行，例如JAVA 虚拟机出现错误。Error是一种unchecked Exception，编译器不会检查Error是否被处理，在程序中不用捕获Error类型的异常。一般情况下，在程序中也不应该抛出Error类型的异常。

##### RuntimeException异常
Exception异常包括RuntimeException异常和其他非RuntimeException的异常。
RuntimeException 是一种Unchecked Exception，即表示编译器不会检查程序是否对RuntimeException作了处理，在程序中不必捕获RuntimException类型的异常，也不必在方法体声明抛出　RuntimeException类。RuntimeException发生的时候，表示程序中出现了编程错误，所以应该找出错误修改程序，而不是去捕获RuntimeException。

##### Checked Exception异常
Checked Exception异常，这也是在编程中使用最多的Exception，所有继承自Exception并且不是RuntimeException的异常都是checked Exception，上图中的IOException和ClassNotFoundException。JAVA 语言规定必须对checked Exception作处理，编译器会对此作检查，要么在方法体中声明抛出checked Exception，要么使用catch语句捕获checked Exception进行处理，不然不能通过编译。

#### spring事务和异常默认关系

##### spring事务的默认配置

Spring的事务管理默认是针对unchecked exception回滚，也就是默认对Error异常和RuntimeException异常以及其子类进行事务回滚，且必须对抛出异常，若使用try-catch对其异常捕获则不会进行回滚！（Error异常和RuntimeException异常抛出时不需要方法调用throws或try-catch语句）；
checked异常,checked异常必须由try-catch语句包含或者由方法throws抛出，且事务默认对checked异常不进行回滚。

##### spring事务回滚异常的修改

###### 注解方式

@javax.transaction.Transactional 和 @org.springframework.transaction.annotation.Transactional 相似，均可使用。
rollbackFor定义的是需要回滚的异常，noRollbackFor定义的是不需要回滚的异常。（默认情况下对Error和RuntimeException及其子类进行回滚）

```
@Transactional(rollbackFor=MyException.class,noRollbackFor=OtherException.class)
public void updateUser(User user){
　　dao.update(user);
}
```

###### 配置方式
定义时声明类的全限定名

```
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <!-- 配置详细事务处理语义 -->
    <tx:attributes>
        <!-- 采用默认事务方式 -->
        <tx:method name="*" rollback-for="com.tz.exception.MyException" no-rollback-for="com.tz.exception.OtherException"/>

    </tx:attributes>
</tx:advice>
```
注：若rollbackFor和noRollbackFor配置的类相同，则出现对应异常会进行回滚


###### 若需要自行捕获异常进行处理

使用 TransactionAspectSupport.currentTransactionStatus().setRollbackOnly() 语句进行手动回滚。

````
@Transactional(rollbackFor=MyException.class,noRollbackFor=RuntimeException.class)
public void updateUser(User user){
    try{
        dao.update(user);
    }catch(MyException e){
        //------//其他操作
        TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();//捕获异常后进行回滚
    }
}
```





