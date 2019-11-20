---
title: Spring基础及组件使用
date: 2019-11-20 12:39:36
update: 2019-11-20 12:39:36
categories: Spring
tags: [spring]
---

### Spring发展历程

2003年2月Spring框架正式称为一道开源项目，Spring致力于J2EE应用的各种解决方案，而不仅仅专注于某一层解决方案。可以说Spring是企业应用开发的“一站式”选择， Spring贯穿于表现层、业务层、持久层，然而Spring并不想取代那些已经有的框架，而是以高度的开放性，与这些已有的框架进行整合。

### Spring目标

1、让现有的技术更容易使用，
2、促进良好的编程习惯。

Spring是一个全面的解决方案，它坚持一个原则：不从新造轮子。已经有较好解决方案的领域，Spring绝不重复性实现，比如：对象持久化和OR映射，Spring只对现有的JDBC，Hibernate等技术提供支持，使之更容易使用，而不做重复的实现。Spring框架有很多特性，这些特性由7个定义良好的模块构成。

### Spring体系结构

1、 Spring Core：即，Spring核心，它是框架最基础的部分，提供IOC和依赖注入特性
2、 Spring Context：即，Spring上下文容器，它是BeanFactory功能加强的一个子接口
3、 Spring Web：它提供Web应用开发的支持
4、 Spring MVC：它针对Web应用中MVC思想的实现
5、 Spring DAO：提供对JDBC抽象层，简化了JDBC编码，同时，编码更具有健壮性。
6、 Spring ORM：它支持用于流行的ORM框架的整合，比如：Spring + Hibernate、Spring + iBatis、Spring + JDO的整合等等。
7、 Spring AOP：AOP即，面向切面编程，它提供了与AOP联盟兼容的编程实现

### Spring常用组件


![Spring基础及组件使用-a](https://volc1612.gitee.io/blog/images/Spring基础及组件使用/Spring基础及组件使用-a.png)

#### 体验Spring的bean管理

```java
import com.enjoy.cap1.Person;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

//配置类====配置文件
@Configuration
public class MainConfig {
    //给容器中注册一个bean, 类型为返回值的类型,
    @Bean("abcPerson")
    public Person person01() {
        return new Person("james", 20);
    }
}
```
```java
import com.enjoy.cap1.config.MainConfig;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MainTest2 {
    public static void main(String args[]) {
        // 把配置文件中的bean加入到spring容器中
        ApplicationContext app = new AnnotationConfigApplicationContext(MainConfig.class);
        //从容器中获取bean
        Person person = (Person) app.getBean("abcPerson");
        System.out.println(person.toString());
        // 获取bean的ID值
        String[] namesForBean = app.getBeanNamesForType(Person.class);
        for (String name : namesForBean) {
            System.out.println(name);
        }
    }
}
```
结果
```
Person [name=james, age=20]
abcPerson
```