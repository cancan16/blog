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

#### 体验Spring的bean注入

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

#### 使用`ComponentScan`注解自定义bean注入

```java
import com.enjoy.cap1.Person;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.ComponentScan.Filter;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;

/**
 * @Description FilterType.CUSTOM, 自定义扫描过滤哪些bean输入到spring容器中，JamesTypeFilter 为自定义过滤方式
 * @Date 15:42 2019/11/20
 */
@Configuration
//@Controller  @Service  @Respostry  @Component
@ComponentScan(value = "com.enjoy.cap2", includeFilters = {
        @Filter(type = FilterType.CUSTOM, classes = {JamesTypeFilter.class})
}, useDefaultFilters = false)
public class Cap2MainConfig {
    //给容器中注册一个bean, 类型为返回值的类型,
    @Bean
    public Person person01() {
        return new Person("james", 20);
    }
}
```

```java
import org.springframework.core.io.Resource;
import org.springframework.core.type.AnnotationMetadata;
import org.springframework.core.type.ClassMetadata;
import org.springframework.core.type.classreading.MetadataReader;
import org.springframework.core.type.classreading.MetadataReaderFactory;
import org.springframework.core.type.filter.TypeFilter;

import java.io.IOException;

public class JamesTypeFilter implements TypeFilter {
    private ClassMetadata classMetadata;

    /*
     * MetadataReader:读取到当前正在扫描类的信息
     * MetadataReaderFactory:可以获取到其他任何类信息
     */

    @Override
    public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory)
            throws IOException {
        //获取当前类注解的信息
        AnnotationMetadata annotationMetadata = metadataReader.getAnnotationMetadata();
        //获取当前正在扫描的类信息
        classMetadata = metadataReader.getClassMetadata();
        //获取当前类资源(类的路径)
        Resource resource = metadataReader.getResource();

        String className = classMetadata.getClassName();
        System.out.println("----->" + className);
        if (className.contains("service")) {// 当类包含order字符, 则匹配成功,返回true
            return true;
        }
        return false;
    }
}
```

测试类：

```java
import com.enjoy.cap2.config.Cap2MainConfig;
import org.junit.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
public class Cap2Test {
    @Test
    public void test01() {
        AnnotationConfigApplicationContext app = new AnnotationConfigApplicationContext(Cap2MainConfig.class);
        String[] names = app.getBeanDefinitionNames();
        for (String name : names) {
            System.out.println(name);
        }
    }
}
```

结果：

```
org.springframework.context.annotation.internalConfigurationAnnotationProcessor
org.springframework.context.annotation.internalAutowiredAnnotationProcessor
org.springframework.context.annotation.internalRequiredAnnotationProcessor
org.springframework.context.annotation.internalCommonAnnotationProcessor
org.springframework.context.event.internalEventListenerProcessor
org.springframework.context.event.internalEventListenerFactory
cap2MainConfig
orderService
person01
```

### @Scope注解

SpringIOC容器中单实例和多实例的区别：
单实例： 在使用对象时创建，并放到IOC容器中。
多实例： 创建IOC容器时，就会创建对象放到IOC容器中。

#### 验证单实例和多实例

```java
import com.enjoy.cap1.Person;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class Cap3MainConfig {
    //给容器中注册一个bean, 类型为返回值的类型, 默认是单实例
    /*
     * prototype:多实例: IOC容器启动的时候,IOC容器启动并不会去调用方法创建对象, 而是每次获取的时候才会调用方法创建对象
     * singleton:单实例(默认):IOC容器启动的时候会调用方法创建对象并放到IOC容器中,以后每次获取的就是直接从容器中拿(大Map.get)的同一个bean
     * request: 主要针对web应用, 递交一次请求创建一个实例
     * session:同一个session创建一个实例
     */
    //@Scope("prototype")
    @Bean
    public Person person() {
        return new Person("james", 20);
    }
}
```

验证
```java
import com.enjoy.cap3.config.Cap3MainConfig;
import org.junit.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Cap3Test {
    @Test
    public void test01() {
        AnnotationConfigApplicationContext app = new AnnotationConfigApplicationContext(Cap3MainConfig.class);


        String[] names = app.getBeanDefinitionNames();

        for (String name : names) {
            System.out.println(name);
        }
        //从容器中分别取两次person实例, 看是否为同一个bean
        Object bean1 = app.getBean("person");
        Object bean2 = app.getBean("person");
        // 比较两个对象的内存地址
        System.out.println(bean1 == bean2);
        //结论:bean1就是bean2,同一个对象
    }
}
```

### @Lazy注解

```java
import com.enjoy.cap1.Person;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Lazy;

@Configuration
public class Cap4MainConfig {
    //给容器中注册一个bean, 类型为返回值的类型, 默认是单实例
    /*
     * 懒加载: 主要针对单实例bean:默认在容器启动的时候创建对象，
     * 容器启动时候不创建对象, 仅当第一次使用(获取)bean的时候才创建被初始化
     */
    @Lazy
    @Bean
    public Person person() {
        System.out.println("给容器中添加person.......");
        return new Person("james", 20);
    }
}

```

验证`Lazy`是否执行了person()方法

```java
import com.enjoy.cap4.config.Cap4MainConfig;
import org.junit.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Cap4Test {
    @Test
    public void test01() {
        AnnotationConfigApplicationContext app = new AnnotationConfigApplicationContext(Cap4MainConfig.class);
        System.out.println("IOC容器创建完成........");
        app.getBean("person");//执行获取的时候才创建并初始化bean
    }
}
```

结果：

```
IOC容器创建完成........
给容器中添加person.......
```

