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

* `spring`容器的`refresh()`[创建刷新]
    * 1，`prepareRefresh()`：刷新前的预处理；
        * 1）`initPropertySources()`：初始化一些属性设置；子类自定义个性化的属性设置方法；
        * 2）`getEnvironment().validateRequiredProperties()`：检验属性的合法性等；
        * 3）`this.earlyApplicationEvents = new LinkedHashSet<>()`保存容器中的一些早期的事件；

    * 2，`obtainFreshBeanFactory()`：获取`beanFactory`实例；
        * 1）`refreshBeanFactory()`刷新或创建`beanFactory`；
        * 2）`getBeanFactory()`：将刚生成的`beanFactory`返回（空的`beanFactory`）；

    * 3，`prepareBeanFactory(beanFactory)`：`beanFactory`预准备工作，（以上创建的`beanFactory`好多属性没有值，只有一些默认的值）；
        * `beanFactory.setBeanClassLoader(getClassLoader())`设置`BeanFactory`的类加载器；
        * `beanFactory.setBeanExpressionResolver`：支持相关的表达式语言的解析；
        * `ignoreDependencyInterface`：设置忽略的自动装配接口 `EnvironmentAware`  `EmbeddedValueResolverAware`；
        * `beanFactory.registerResolvableDependency`：注册可以解析的自动装配：我们能直接在任何组件中自动注入  `BeanFactory`  `ApplicationContext`；
        * `beanFactory.addBeanPostProcessor(new ApplicationListenerDetector(this))`添加监听检测的处理器；
        * `if (!beanFactory.containsLocalBean(ENVIRONMENT_BEAN_NAME))`：注册环境变量相关`bean`；
    * 4，`postProcessBeanFactory(beanFactory)` `BeanFactory`准备工作完成后进行的后置处理器工作；
        * 1， 通过子类重写这个方法来在`BeanFactory`创建并预准备完成以后进一步的设置；至此`BeanFactory`创建和预准备完成；

    * 5，`invokeBeanFactoryPostProcessors(beanFactory)`执行`BeanFactoryPostProcessor`后置处理器方法；
        * 自定义实现两个接口`BeanFactoryPostProcessor`，`BeanDefinitionRegistryPostProcessor`;
        * 先执行`BeanDefinitionRegistryPostProcessor`；
            * 优先执行实现了`PriorityOrdered`的处理器；
            * 获取处理器`bean`对象；
    * 6，`initMessageSource()`标签国际化资源，初始化`messgeresource`组件（国际化功能：消息解析，消息绑定）；
        * `if (beanFactory.containsLocalBean(MESSAGE_SOURCE_BEAN_NAME))`判断容器中是否有`messageSource`的`ID`，类型是`MessageSource`组件；
        * `new DelegatingMessageSource()`如果没有`MessageSource`组件就会创建并注册到`IOC`容器中；
        * `registerSingleton(MESSAGE_SOURCE_BEAN_NAME, this.messageSource)`注册`MessageSource`组件；
    * 7，`initApplicationEventMulticaster()`初始化事件派发器；
        * `getBeanFactory()`获取`beanFactory`；
        * `if (beanFactory.containsLocalBean(APPLICATION_EVENT_MULTICASTER_BEAN_NAME))`判断有没有`applicationEventMulticaster`对象；
        * `new SimpleApplicationEventMulticaster(beanFactory)`创建事件派发器；
        * `registerSingleton(APPLICATION_EVENT_MULTICASTER_BEAN_NAME, this.applicationEventMulticaster)`注册到容器中；
    * 8，`onRefresh()`留给子容器（子类）：子类可以重写这个方法，在容器刷新的时候自定义逻辑；
    * 9，`registerListeners()`给容器中将所有项目里的ApplicationListener注册进来；
        * `getApplicationEventMulticaster().multicastEvent(earlyEvent)`事件派发；
    * 10，`finishBeanFactoryInitialization(beanFactory)`给容器中将所有项目中单实例`bean`(非懒加载)初始化；
        * `beanFactory.preInstantiateSingletons()`单实例bean(非懒加载)初始化；
            * `getMergedLocalBeanDefinition(beanName)`获取bean的定义信息，依次进行创建和初始化；
                * `getBean(beanName)`；
                    * `doGetBean(name, null, null, false)`；
                        * `getSingleton(beanName)`先获取`map`缓存中保存的实例`bean`；
                        * `markBeanAsCreated(beanName)`标记当前`bean`已经被创建了；
                            * `this.alreadyCreated.add(beanName)`进行标记；
                        * `mbd.getDependsOn()`获取当前`bean`依赖的其他`bean`，如果存在就从容器中获取依赖的`bean`；
                        * `createBean(beanName, mbd, args)`；
                            * `resolveBeforeInstantiation(beanName, mbdToUse)`让`BeanPostProcessor`尝试返回一个代理对象；
                            * `doCreateBean(beanName, mbdToUse, args)`；
                                * `createBeanInstance(beanName, mbd, args)`创建`bean`实例返回`bean`包装类型；
                                * `populateBean(beanName, mbd, instanceWrapper)``bean`属性赋值；
                                    * `if (bp instanceof InstantiationAwareBeanPostProcessor)`后置处理器的处理；
                                    * `postProcessAfterInstantiation(bw.getWrappedInstance(), beanName)`调用后置处理器对`bean`进行定制处理；
                                    * `applyPropertyValues(beanName, mbd, bw, pvs)`对`bean`设置值；
                                * `initializeBean(beanName, exposedObject, mbd)`前后处理器执行的地方，`aop`动态代理增强入口；
                                    * `applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName)`bean初始化前置处理器；
                                    * `applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName)`bean初始化后置处理器；
    * 11，`finishRefresh()`结束容器刷新；






















