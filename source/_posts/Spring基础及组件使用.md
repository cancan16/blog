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

<!-- more -->

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

bean注入到IOC容器中方式:

* 1,@Bean: [导入第三方的类或包的组件],比如Person为第三方的类, 需要在我们的IOC容器中使用
* 2,包扫描+组件的标注注解(@ComponentScan:  @Controller, @Service  @Reponsitory  @ Componet),一般是针对 我们自己写的类,使用这个
* 3,@Import:[快速给容器导入一个组件] 注意:@Bean有点简单
    a,@Import(要导入到容器中的组件):容器会自动注册这个组件,bean 的 id为全类名
    b,ImportSelector:是一个接口,返回需要导入到容器的组件的全类名数组
    c,ImportBeanDefinitionRegistrar:可以手动添加组件到IOC容器, 所有Bean的注册可以使用BeanDifinitionRegistry
        写JamesImportBeanDefinitionRegistrar实现ImportBeanDefinitionRegistrar接口即可
* 4,使用Spring提供的FactoryBean(工厂bean)进行注册

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

* 对于单实例的bean, 可以正常调用初始化和销毁方法
* 对于多实例的bean, 容器只负责初始化, 但不会管理bean, 容器关闭时不会调用销毁方法

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

### @Conditional条件注册bean

当操作系统为WINDOWS时,注册Lison实例; 当操作系统为LINUX时, 注册James实例

`FactoryBean`和`BeanFactory`的区别
Java实例化bean时，通过`FactoryBean`注入到IOC容器中，通过`BeanFactory`从 IOC容器中获取实例化的bean。

```java
import com.enjoy.cap1.Person;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Conditional;
import org.springframework.context.annotation.Configuration;

@Configuration
public class Cap5MainConfig {
    @Bean("person")
    public Person person() {
        System.out.println("给容器中添加person.......");
        return new Person("person", 20);
    }

    @Conditional(WinCondition.class)
    @Bean("lison")
    public Person lison() {
        System.out.println("给容器中添加lison.......");
        return new Person("Lison", 58);
    }

    @Conditional(LinCondition.class)
    @Bean("james")//bean在容器中的ID为james, IOC容器MAP,  map.put("id",value)
    public Person james() {
        System.out.println("给容器中添加james.......");
        return new Person("james", 20);
    }
}
```

```java
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
import org.springframework.context.annotation.Condition;
import org.springframework.context.annotation.ConditionContext;
import org.springframework.core.env.Environment;
import org.springframework.core.type.AnnotatedTypeMetadata;

public class LinCondition implements Condition {
    /*
     *ConditionContext: 判断条件可以使用的上下文(环境)
     *AnnotatedTypeMetadata: 注解的信息
     *
     */
    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        // TODO 是否为WINDOW系统
        //能获取到IOC容器正在使用的beanFactory
        ConfigurableListableBeanFactory beanFactory = context.getBeanFactory();
        //获取当前环境变量(包括我们操作系统是WIN还是LINUX??)
        Environment environment = context.getEnvironment();
        String os_name = environment.getProperty("os.name");
        if (os_name.contains("linux")) {
            return true;
        }
        return false;
    }
}
```

#### 使用ImportSelector自定义返回组件

```java
import org.springframework.context.annotation.ImportSelector;
import org.springframework.core.type.AnnotationMetadata;

public class JamesImportSelector implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        //返回全类名的bean
        return new String[]{"com.enjoy.cap6.bean.Fish", "com.enjoy.cap6.bean.Tiger"};
    }
}
```

```java
import com.enjoy.cap1.Person;
import com.enjoy.cap6.bean.Cat;
import com.enjoy.cap6.bean.Dog;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration
@Import(value = {Dog.class, Cat.class, JamesImportSelector.class, JamesImportBeanDefinitionRegistrar.class})
public class Cap6MainConfig {
    /*
     * 给容器中注册组件的方式
     * 1,@Bean: [导入第三方的类或包的组件],比如Person为第三方的类, 需要在我们的IOC容器中使用
     * 2,包扫描+组件的标注注解(@ComponentScan:  @Controller, @Service  @Reponsitory  @ Componet),一般是针对 我们自己写的类,使用这个
     * 3,@Import:[快速给容器导入一个组件] 注意:@Bean有点简单
     *      a,@Import(要导入到容器中的组件):容器会自动注册这个组件,bean 的 id为全类名
     *      b,ImportSelector:是一个接口,返回需要导入到容器的组件的全类名数组
     *      c,ImportBeanDefinitionRegistrar:可以手动添加组件到IOC容器, 所有Bean的注册可以使用BeanDifinitionRegistry
     *          写JamesImportBeanDefinitionRegistrar实现ImportBeanDefinitionRegistrar接口即可
     *  4,使用Spring提供的FactoryBean(工厂bean)进行注册
     *
     *
     */
}
```



#### 使用ImportBeanDefinitionRegistrar返回自定义组件

```java
import com.enjoy.cap6.bean.Pig;
import org.springframework.beans.factory.support.BeanDefinitionRegistry;
import org.springframework.beans.factory.support.RootBeanDefinition;
import org.springframework.context.annotation.ImportBeanDefinitionRegistrar;
import org.springframework.core.type.AnnotationMetadata;

public class JamesImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {

    /*
     *AnnotationMetadata:当前类的注解信息
     *BeanDefinitionRegistry:BeanDefinition注册类
     *    把所有需要添加到容器中的bean加入;
     *    @Scope
     */
    @Override
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
        boolean bean1 = registry.containsBeanDefinition("com.enjoy.cap6.bean.Dog");
        boolean bean2 = registry.containsBeanDefinition("com.enjoy.cap6.bean.Cat");
        //如果Dog和Cat同时存在于我们IOC容器中,那么创建Pig类, 加入到容器
        //对于我们要注册的bean, 给bean进行封装,
        if (bean1 && bean2) {
            RootBeanDefinition beanDefinition = new RootBeanDefinition(Pig.class);
            registry.registerBeanDefinition("pig", beanDefinition);
        }
    }
}
```


```java
import com.enjoy.cap1.Person;
import com.enjoy.cap6.bean.Cat;
import com.enjoy.cap6.bean.Dog;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration
@Import(value = {Dog.class, Cat.class, JamesImportSelector.class, JamesImportBeanDefinitionRegistrar.class})
public class Cap6MainConfig {
    /*
     * 给容器中注册组件的方式
     * 1,@Bean: [导入第三方的类或包的组件],比如Person为第三方的类, 需要在我们的IOC容器中使用
     * 2,包扫描+组件的标注注解(@ComponentScan:  @Controller, @Service  @Reponsitory  @ Componet),一般是针对 我们自己写的类,使用这个
     * 3,@Import:[快速给容器导入一个组件] 注意:@Bean有点简单
     *      a,@Import(要导入到容器中的组件):容器会自动注册这个组件,bean 的 id为全类名
     *      b,ImportSelector:是一个接口,返回需要导入到容器的组件的全类名数组
     *      c,ImportBeanDefinitionRegistrar:可以手动添加组件到IOC容器, 所有Bean的注册可以使用BeanDifinitionRegistry
     *          写JamesImportBeanDefinitionRegistrar实现ImportBeanDefinitionRegistrar接口即可
     *  4,使用Spring提供的FactoryBean(工厂bean)进行注册
     *
     *
     */
}
```

#### Factorybean工厂bean注入IOC容器中

把`Monkey`对象实例化到容器中

```java
import com.enjoy.cap6.bean.Monkey;
import org.springframework.beans.factory.FactoryBean;

public class JamesFactoryBean implements FactoryBean<Monkey> {

    @Override
    public Monkey getObject() throws Exception {
        // TODO Auto-generated method stub
        return new Monkey();
    }

    @Override
    public Class<?> getObjectType() {
        // TODO Auto-generated method stub
        return Monkey.class;
    }

    @Override
    public boolean isSingleton() {
        return true;
    }
}
```

```java
import com.enjoy.cap1.Person;
import com.enjoy.cap6.bean.Cat;
import com.enjoy.cap6.bean.Dog;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration
@Import(value = {Dog.class, Cat.class, JamesImportSelector.class, JamesImportBeanDefinitionRegistrar.class})
public class Cap6MainConfig {
    /*
     * 给容器中注册组件的方式
     * 1,@Bean: [导入第三方的类或包的组件],比如Person为第三方的类, 需要在我们的IOC容器中使用
     * 2,包扫描+组件的标注注解(@ComponentScan:  @Controller, @Service  @Reponsitory  @ Componet),一般是针对 我们自己写的类,使用这个
     * 3,@Import:[快速给容器导入一个组件] 注意:@Bean有点简单
     *      a,@Import(要导入到容器中的组件):容器会自动注册这个组件,bean 的 id为全类名
     *      b,ImportSelector:是一个接口,返回需要导入到容器的组件的全类名数组
     *      c,ImportBeanDefinitionRegistrar:可以手动添加组件到IOC容器, 所有Bean的注册可以使用BeanDifinitionRegistry
     *          写JamesImportBeanDefinitionRegistrar实现ImportBeanDefinitionRegistrar接口即可
     * 4,使用Spring提供的FactoryBean(工厂bean)进行注册
     *
     *
     */
    @Bean
    public JamesFactoryBean jamesFactoryBean() {
        return new JamesFactoryBean();
    }
}
```

### springbean的声明周期-初始化和销毁

Bean的生命周期指Bean创建-->初始化-->销毁 的过程

* 对于单实例的bean, 可以正常调用初始化和销毁方法
* 对于多实例的bean, 容器只负责初始化, 但不会管理bean, 容器关闭时不会调用销毁方法

#### 指定bean的初始化和销毁方法

```java
public class Bike {
    public Bike() {
        System.out.println("Bike constructor..............");
    }

    public void init() {
        System.out.println("Bike .....init.....");
    }

    public void destory() {
        System.out.println("Bike.....destory");
    }
}
```

```java
import com.enjoy.cap1.Person;
import com.enjoy.cap7.bean.Bike;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
public class Cap7MainConfigOfLifeCycle {
    /**
     * @Date 17:22 2019/12/5
     * @Description 指定初始化方法和销毁方法
     * @Return com.enjoy.cap7.bean.Bike
     * @Param []
     */
    @Bean(initMethod = "init", destroyMethod = "destory")
    public Bike bike() {
        return new Bike();
    }
}
```

测试

```java
import com.enjoy.cap7.config.Cap7MainConfigOfLifeCycle;
import org.junit.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Cap7Test {
    @Test
    public void test01() {
        AnnotationConfigApplicationContext app = new AnnotationConfigApplicationContext(Cap7MainConfigOfLifeCycle.class);
        System.out.println("IOC容器创建完成........");
        app.close();
    }
}
```

```
Bike constructor..............
Bike .....init.....
IOC容器创建完成........
十二月 06, 2019 2:59:56 下午 org.springframework.context.support.AbstractApplicationContext doClose
信息: Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@5c0369c4: startup date [Fri Dec 06 14:59:55 CST 2019]; root of context hierarchy
Bike.....destory
```

#### 实现InitializingBean接口和DisposableBean接口实现bean的初始化和销毁

```java
import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.stereotype.Component;

@Component
public class Train implements InitializingBean, DisposableBean {

    public Train() {
        System.out.println("Train......constructor............");
    }

    // 当我们bean销毁时,调用此方法
    @Override
    public void destroy() throws Exception {
        System.out.println("Train......destory......");
        //logger.error
    }

    /**
     * @Date 15:17 2019/12/6
     * @Description 所有属性设置好之后, 会调这个方法, 相当于初始化方法
     * @Return void
     * @Param []
     */
    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("Train.......afterPropertiesSet()...");
    }
}
```

#### 使用JSR250规则定义的(java规范)两个注解来实现`@PostConstruct` `@PreDestroy`

@PostConstruct: 在Bean创建完成,且属于赋值完成后进行初始化,属于JDK规范的注解
@PreDestroy: 在bean将被移除之前进行通知, 在容器销毁之前进行清理工作


```java
import org.springframework.stereotype.Component;
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

@Component
public class Jeep {
    public Jeep() {
        System.out.println("Jeep.....constructor........");
    }

    @PostConstruct
    public void init() {
        System.out.println("Jeep.....@PostConstruct........");
    }

    @PreDestroy
    public void destory() {
        System.out.println("Jeep.....@PreDestroy......");
    }
}
```

#### 使用BeanPostProcessors控制bean的声明周期

实现BeanPostProcessors的两个接口即可
1,  postProcessBeforeInitialization()
2，postProcessAfterInitialization()

```java
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;
import org.springframework.stereotype.Component;

@Component
public class JamesBeanPostProcessor implements BeanPostProcessor {
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        //返回一个的对象(传过来的对象)
        //在初始化方法调用之前进行后置处理工作,
        //什么时候调用它: init-method=init之前调用
        System.out.println("postProcessBeforeInitialization...." + beanName + "..." + bean);
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("postProcessAfterInitialization...." + beanName + "..." + bean);
        return bean;
    }
}
```

```java
public class Bike {
    public Bike() {
        System.out.println("Bike constructor..............");
    }

    public void init() {
        System.out.println("Bike .....init.....");
    }

    public void destory() {
        System.out.println("Bike.....destory");
    }
}
```

```java
import com.enjoy.cap7.bean.Bike;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@ComponentScan("com.enjoy.cap7.bean")
@Configuration
public class Cap7MainConfigOfLifeCycle {
    /**
     * @Date 17:22 2019/12/5
     * @Description 指定初始化方法和销毁方法
     * @Return com.enjoy.cap7.bean.Bike
     * @Param []
     */
    @Bean(initMethod = "init", destroyMethod = "destory")
    public Bike bike() {
        return new Bike();
    }
}
```

结果
```
Bike constructor..............
postProcessBeforeInitialization....bike...com.enjoy.cap7.bean.Bike@25d250c6
Bike .....init.....
postProcessAfterInitialization....bike...com.enjoy.cap7.bean.Bike@25d250c6
IOC容器创建完成........
十二月 06, 2019 3:34:17 下午 org.springframework.context.support.AbstractApplicationContext doClose
信息: Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@5c0369c4: startup date [Fri Dec 06 15:34:16 CST 2019]; root of context hierarchy
Bike.....destory
```

可以看出重写BeanPostProcessor后，所有的初始化方法之前会调用`postProcessBeforeInitialization`，初始化之后会调用`postProcessAfterInitialization`


