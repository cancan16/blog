---
title: Spring组件扩展以及IOC源码分析
date: 2019-12-17 11:29:36
update: 2019-12-17 11:29:36
categories: Spring
tags: [spring,ioc]
---

<div style="text-align: center"><iframe height="60" width="260" src="https://www.ximalaya.com/thirdparty/player/sound/player.html?id=6549547&type=red" frameborder=0 allowfullscreen></iframe></div>

<img style="width: 80%;height:80%" src="https://volc1612.gitee.io/blog/images/Spring组件扩展以及IOC源码分析/dog.jpg" />

<!-- more -->

### BeanFactoryPostProcessor: beanFactory的后置处理器

### 业务bean实现bean工厂后置处理器后，业务bean的创建过程

* 业务bean实现`BeanFactoryPostProcessor`接口

```java
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanFactoryPostProcessor;
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
import org.springframework.stereotype.Component;
import java.util.Arrays;

@Component
public class JamesBeanFactoryPostProcessor implements BeanFactoryPostProcessor {

    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
        System.out.println("JamesBeanFactoryPostProcessor.....调到了BeanFactoryPostProcessor.postProcessBeanFactory()");
        //所有bean的定义，已经加载到beanFactory, 但是bean实例还没创建
        int count = beanFactory.getBeanDefinitionCount();
        String[] beanDefName = beanFactory.getBeanDefinitionNames();
        System.out.println("当前BeanFactory中有" + count + "个Bean");
        System.out.println(Arrays.asList(beanDefName));
    }
}
```

* 把业务bean交给`IOC`管理

```java
import com.enjoy.cap9.bean.Moon;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan("com.enjoy.cap12.processor")
public class Cap12MainConfig {
    @Bean
    public Moon getMoon() {
        return new Moon();
    }
}
```

* 查看业务bean实现bean工厂后置处理器接口后创建过程

```java
import com.enjoy.cap12.Cap12MainConfig;
import org.junit.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Cap12Test {
    @Test
    public void test01() {
        AnnotationConfigApplicationContext app = new AnnotationConfigApplicationContext(Cap12MainConfig.class);
        app.close();
    }
}
```

* 结果

```
JamesBeanFactoryPostProcessor.....调到了BeanFactoryPostProcessor.postProcessBeanFactory()
当前BeanFactory中有9个Bean
[org.springframework.context.annotation.internalConfigurationAnnotationProcessor, org.springframework.context.annotation.internalAutowiredAnnotationProcessor, org.springframework.context.annotation.internalRequiredAnnotationProcessor, org.springframework.context.annotation.internalCommonAnnotationProcessor, org.springframework.context.event.internalEventListenerProcessor, org.springframework.context.event.internalEventListenerFactory, cap12MainConfig, jamesBeanFactoryPostProcessor, getMoon]
十二月 19, 2019 2:42:12 下午 org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor <init>
信息: JSR-330 'javax.inject.Inject' annotation found and supported for autowiring
Moon constructor........
```

**总结**

业务`bean`在创建时，先把业务bean定义到`BeanFactory`中，然后执行`bean`后置处理器，最后创建实例交给`IOC`容器管理。
在`beanFactory`标准初始化之后调用，来制定和修改`beanFactory`中的内容。

### BeanDefinitionRegistryPostProcessor: bean定义后置处理器

* 业务bean实现`BeanDefinitionRegistryPostProcessor`接口

```java
import com.enjoy.cap9.bean.Moon;
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
import org.springframework.beans.factory.support.AbstractBeanDefinition;
import org.springframework.beans.factory.support.BeanDefinitionBuilder;
import org.springframework.beans.factory.support.BeanDefinitionRegistry;
import org.springframework.beans.factory.support.BeanDefinitionRegistryPostProcessor;
import org.springframework.stereotype.Component;

@Component
public class JamesBeanDefinitionRegistryPostProcessor implements BeanDefinitionRegistryPostProcessor {
    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
        System.out.println("JamesBeanDefinitionProcessor..postProcessBeanFactory(),Bean的数量" + beanFactory.getBeanDefinitionCount());
    }
    /**
     * @Date 15:09 2019/12/19
     * @Description 执行该方法，可以自定义注册别的bean
     * @Return void
     * @Param [registry]
     */
    @Override
    public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry) throws BeansException {
        System.out.println("amesBeanDefinition.postProcessBeanDefinitionRegistry()...bean的数量" + registry.getBeanDefinitionCount());
        // 注册其他bean
        //RootBeanDefinition rbd = new RootBeanDefinition(Moon.class);
        AbstractBeanDefinition rbd = BeanDefinitionBuilder.rootBeanDefinition(Moon.class).getBeanDefinition();
        registry.registerBeanDefinition("hello", rbd);
    }
}
```

* 把业务bean交给`IOC`管理

```java
import com.enjoy.cap9.bean.Moon;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan("com.enjoy.cap12.processor")
public class Cap12MainConfig {
    @Bean
    public Moon getMoon() {
        return new Moon();
    }
}
```

* 业务bean实现bean定义后置处理器执行过程

```java
import com.enjoy.cap12.Cap12MainConfig;
import org.junit.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Cap12Test {
    @Test
    public void test01() {
        AnnotationConfigApplicationContext app = new AnnotationConfigApplicationContext(Cap12MainConfig.class);
        app.close();
    }
}
```

* 结果

```
amesBeanDefinition.postProcessBeanDefinitionRegistry()...bean的数量9
JamesBeanDefinitionProcessor..postProcessBeanFactory(),Bean的数量10
十二月 19, 2019 3:34:37 下午 org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor <init>
信息: JSR-330 'javax.inject.Inject' annotation found and supported for autowiring
Moon constructor........
Moon constructor........
```

* 总结

实现`BeanDefinitionRegistryPostProcessor`接口的业务bean，先执行自定义bean定义方法，再执行`postProcessBeanFactory`注入到IOC容器中。

### spring容器的refresh方法

* spring容器的refresh()[创建刷新]
    * 1，prepareRefresh()：刷新前的预处理
        * 1）initPropertySources()：初始化一些属性设置；子类自定义个性化的属性设置方法
        * 2）getEnvironment().validateRequiredProperties()：检验属性的合法性等
        * 3）this.earlyApplicationEvents = new LinkedHashSet<>();保存容器中的一些早期的事件

    * 2，obtainFreshBeanFactory()：获取beanFactory实例
        * 1）refreshBeanFactory();刷新或创建beanFactory
        * 2）getBeanFactory()：将刚生成的beanFactory返回（空的beanFactory）

    * 3，prepareBeanFactory(beanFactory)：beanFactory预准备工作，（以上创建的beanFactory好多属性没有值，只有一些默认的值）
        * beanFactory.setBeanClassLoader(getClassLoader());设置BeanFactory的类加载器
        * beanFactory.setBeanExpressionResolver：支持相关的表达式语言的解析
        * ignoreDependencyInterface：设置忽略的自动装配接口 EnvironmentAware  EmbeddedValueResolverAware
        * beanFactory.registerResolvableDependency：注册可以解析的自动装配：我们能直接在任何组件中自动注入  BeanFactory  ApplicationContext
        * beanFactory.addBeanPostProcessor(new ApplicationListenerDetector(this));添加监听检测的处理器
        * if (!beanFactory.containsLocalBean(ENVIRONMENT_BEAN_NAME))：注册环境变量相关bean
    * 4， postProcessBeanFactory(beanFactory);BeanFactory准备工作完成后进行的后置处理器工作；
        * 1， 通过子类重写这个方法来在BeanFactory创建并预准备完成以后进一步的设置；至此BeanFactory创建和预准备完成。

    * 5， invokeBeanFactoryPostProcessors(beanFactory)执行BeanFactoryPostProcessor后置处理器方法。
        * 自定义实现两个接口BeanFactoryPostProcessor，BeanDefinitionRegistryPostProcessor;
        * 先执行`BeanDefinitionRegistryPostProcessor`
            * 优先执行实现了`PriorityOrdered`的处理器；
            * 获取处理器bean对象；





















