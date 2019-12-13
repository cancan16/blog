---
title: Spring组件及AOP底层源码
date: 2019-12-10 12:39:36
update: 2019-12-10 12:39:36
categories: Spring
tags: [aop]
---

<div style="text-align: center"><iframe height="60" width="260" src="https://www.ximalaya.com/thirdparty/player/sound/player.html?id=6549547&type=red" frameborder=0 allowfullscreen></iframe></div>

<img style="width: 80%;height:80%" src="https://volc1612.gitee.io/blog/images/Spring组件及AOP底层源码/dog.jpeg" />


<!-- more -->

**思维导图**

![Spring组件及AOP底层源码](https://volc1612.gitee.io/blog/images/Spring组件及AOP底层源码/spring-aop源码分析.png)


### AOP

AOP: 面向切面编程[底层就是动态代理]
指程序在运行期间动态的将某段代码切入到指定方法位置进行运行的编程方式。

* AOP通知方式

前置通知: logStart(),在目标方法(div)运行之前运行 (@Before)
后置通知:logEnd(), 在目标方法(div)运行结束之后运行,无论正常或异常结束 (@After)
返回通知:logReturn, 在目标方法(div)正常返回之后运行 (@AfterReturning)
异常通知:logException, 在目标方法(div)出现异常后运行(@AfterThrowing)
环绕通知:动态代理, 最底层通知,手动指定执行目标方法(@Around)

```java
public class Calculator {
    //业务逻辑方法
    public int div(int i, int j) {
        System.out.println("--------");
        return i / j;
    }
}
```

```java
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;

//日志切面类
@Aspect
public class LogAspects {
    @Pointcut("execution(public int com.enjoy.cap10.aop.Calculator.*(..))")
    public void pointCut() {
    }

    //@before代表在目标方法执行前切入, 并指定在哪个方法前切入
    @Before("pointCut()")
    public void logStart() {
        System.out.println("除法运行....参数列表是:{}");
    }

    @After("pointCut()")
    public void logEnd() {
        System.out.println("除法结束......");

    }

    @AfterReturning("pointCut()")
    public void logReturn() {
        System.out.println("除法正常返回......运行结果是:{}");
    }

    @AfterThrowing("pointCut()")
    public void logException() {
        System.out.println("运行异常......异常信息是:{}");
    }

    @Around("pointCut()")
    public Object Around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("@Arount:执行目标方法之前...");
        Object obj = proceedingJoinPoint.proceed();//相当于开始调div地
        System.out.println("@Arount:执行目标方法之后...");
        return obj;
    }
}
```

注入到IOC容器中

```java
import com.enjoy.cap10.aop.Calculator;
import com.enjoy.cap10.aop.LogAspects;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

/*
 * 日志切面类的方法需要动态感知到div()方法运行,
 *  通知方法:
 *     前置通知:logStart(); 在我们执行div()除法之前运行(@Before)
 *     后置通知:logEnd();在我们目标方法div运行结束之后 ,不管有没有异常(@After)
 *     返回通知:logReturn();在我们的目标方法div正常返回值后运行(@AfterReturning)
 *     异常通知:logException();在我们的目标方法div出现异常后运行(@AfterThrowing)
 *     环绕通知:动态代理, 需要手动执行joinPoint.procced()(其实就是执行我们的目标方法div,), 执行之前div()相当于前置通知, 执行之后就相当于我们后置通知(@Around)
 */
@Configuration
@EnableAspectJAutoProxy
public class Cap10MainConfig {
    @Bean
    public Calculator calculator() {
        return new Calculator();
    }

    @Bean
    public LogAspects logAspects() {
        return new LogAspects();
    }
}
```

测试类，需要从容器中拿

```java
import com.enjoy.cap10.aop.Calculator;
import com.enjoy.cap10.config.Cap10MainConfig;
import org.junit.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Cap10Test {
    @Test
    public void test01() {
        AnnotationConfigApplicationContext app = new AnnotationConfigApplicationContext(Cap10MainConfig.class);

        //Calculator c = new Calculator();
        Calculator c = app.getBean(Calculator.class);
        int result = c.div(4, 3);
        System.out.println(result);
        app.close();
    }
}
```

```
@Arount:执行目标方法之前...
除法运行....参数列表是:{}
--------
@Arount:执行目标方法之后...
除法结束......
除法正常返回......运行结果是:{}
1
```

### AnnotationAwareAspectJAutoProxyCreator核心类过程分析

![AnnotationAwareAspectJAutoProxyCreator引用层次](https://volc1612.gitee.io/blog/images/Spring组件及AOP底层源码/AnnotationAwareAspectJAutoProxyCreator引用层次.png)

`org.springframework.aop.aspectj.annotation.AnnotationAwareAspectJAutoProxyCreator`这个类贯穿了整AOP功能。

AnnotationAwareAspectJAutoProxyCreator：
  	AnnotationAwareAspectJAutoProxyCreator
  	   ->AspectJAwareAdvisorAutoProxyCreator
  		  ->AbstractAdvisorAutoProxyCreator
  			 ->AbstractAutoProxyCreator
  			   implements SmartInstantiationAwareBeanPostProcessor, BeanFactoryAware
**重点关注后置处理器（在bean初始化完成前后做事情）、自动装配BeanFactory**

SmartInstantiationAwareBeanPostProcessor: bean的后置处理器
BeanFactoryAware：能把beanFacotry bean工厂传进来
通过分析以上的bean继承关系我们发现,   具有BeanPostProcessor特点, 也有Aware接口的特点, 实现了BeanFactoryAware 接口。

#### 创建和注册AnnotationAwareAspectJAutoProxyCreator流程

分析创建和注册AnnotationAwareAspectJAutoProxyCreator的流程:
* 1）、register()传入配置类，准备创建ioc容器
* 2）、注册配置类，调用refresh（）刷新创建容器；
* 3）、registerBeanPostProcessors(beanFactory);注册bean的后置处理器来方便拦截bean的创建(主要是分析创建AnnotationAwareAspectJAutoProxyCreator)；
 	* 1）、先获取ioc容器已经定义了的需要创建对象的所有BeanPostProcessor
 	* 2）、给容器中加别的BeanPostProcessor
 	* 3）、优先注册实现了PriorityOrdered接口的BeanPostProcessor；
 	* 4）、再给容器中注册实现了Ordered接口的BeanPostProcessor；
 	* 5）、注册没实现优先级接口的BeanPostProcessor；
 	* 6）、注册BeanPostProcessor，实际上就是创建BeanPostProcessor对象，保存在容器中；
 		创建internalAutoProxyCreator的BeanPostProcessor【其实就是AnnotationAwareAspectJAutoProxyCreator】
 		* 1）、创建Bean的实例
 		* 2）、populateBean；给bean的各种属性赋值
 		* 3）、initializeBean：初始化bean；
            * 1）、invokeAwareMethods()：处理Aware接口的方法回调
            * 2）、applyBeanPostProcessorsBeforeInitialization()：应用后置处理器的postProcessBeforeInitialization（）
            * 3）、invokeInitMethods()；执行自定义的初始化方法
            * 4）、applyBeanPostProcessorsAfterInitialization()；执行后置处理器的postProcessAfterInitialization（）；
 		* 4）、BeanPostProcessor(AnnotationAwareAspectJAutoProxyCreator)创建成功；--》aspectJAdvisorsBuilder
 	* 7）、把BeanPostProcessor注册到BeanFactory中；
 		beanFactory.addBeanPostProcessor(postProcessor);

注意:以上是创建和注册AnnotationAwareAspectJAutoProxyCreator的过程`AnnotationAwareAspectJAutoProxyCreator`=>`InstantiationAwareBeanPostProcessor`

#### 创建增强的Caculator(业务bean)增强bean的流程

二, 如何创建增强的Caculator增强bean的流程:
refresh--->finishBeanFactoryInitialization(beanFactory);完成BeanFactory初始化工作；创建剩下的单实例bean。

* 1）、遍历获取容器中所有的Bean，依次创建对象getBean(beanName);
            getBean->doGetBean()->getSingleton()->
* 2）、创建bean
            【AnnotationAwareAspectJAutoProxyCreator在所有bean创建之前会有一个拦截，InstantiationAwareBeanPostProcessor，会调用postProcessBeforeInstantiation()】
    * 2.1）、先从缓存中获取当前bean，如果能获取到，说明bean是之前被创建过的，直接使用，否则再创建；
                只要创建好的Bean都会被缓存起来
    * 2.2）、createBean（）;创建bean；
                AnnotationAwareAspectJAutoProxyCreator 会在任何bean创建之前先尝试返回bean的实例
                【BeanPostProcessor是在Bean对象创建完成初始化前后调用的】
                【InstantiationAwareBeanPostProcessor是在创建Bean实例之前先尝试用后置处理器返回对象的】
        * 2.2.1）、resolveBeforeInstantiation(beanName, mbdToUse);解析BeforeInstantiation,如果能返回代理对象就使用，如果不能就继续,后置处理器先尝试返回对象；
        bean = applyBeanPostProcessorsBeforeInstantiation（）：
        拿到所有后置处理器，如果是InstantiationAwareBeanPostProcessor;
        就执行postProcessBeforeInstantiation
        if (bean != null) {
                bean = applyBeanPostProcessorsAfterInitialization(bean, beanName);
        }

        * 2.2.2）、doCreateBean(beanName, mbdToUse, args);真正的去创建一个bean实例；和单实例bean创建流程一样；

**回顾bean创建过程**
Bean创建流程回顾:
reresh() --> registerBeanPostProcessors-->getBean()--->doGetBean()-->createBean(beanName, mbd, args)--->bean创建--->bean初始化--->后置处理器工作--->

**回顾业务bean的创建代理对象过程**

主要判断当前类是否需要增强, 如果需要增强, 则动态代理创建增强代理对象
单实例caculator创建--->createBean()-->resolveBeforeInstantiation()-->
applyBeanPostProcessorsBeforeInstantiation--->postProcessBeforeInstantiation()--->
new Caculator()创建对象--->进入1701行:applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName)--->调用postProcessAfterInstantiation()--->
调用getAdvicesAndAdvisorsForBean()获取当前bean的通知方法--->
得到由CGLIB增强后的的caculator-->
当执行目标方法时,CGLIB代理对象就会调用之前保存好的切面通知

### AOP调用流程

目标方法执行（caculator.div()方法执行切面拦截）；容器中保存了组件的代理对象（cglib增强后的对象），这个对象里面保存了详细信息（比如增强器，目标对象，xxx）；

* 1）、CglibAopProxy.intercept();拦截目标方法的执行
* 2）、根据ProxyFactory对象获取将要执行的目标方法拦截器链；List`<`Object`>` chain = this.advised.getInterceptorsAndDynamicInterceptionAdvice(method, targetClass);
    * 1）、List`<`Object`>` interceptorList保存所有拦截器5个，一个默认的`ExposeInvocationInterceptor`和4个增强器；
    * 2）、遍历所有的增强器，将其转为Interceptor；registry.getInterceptors(advisor)；
    * 3）、将增强器转为List`<`MethodInterceptor`>`；如果是MethodInterceptor，直接加入到集合中，如果不是，使用AdvisorAdapter将增强器转为MethodInterceptor；转换完成返回MethodInterceptor数组；
* 3）、如果没有拦截器链，直接执行目标方法;拦截器链（每一个通知方法又被包装为方法拦截器，利用MethodInterceptor机制）
* 4）、如果有拦截器链，把需要执行的目标对象，目标方法，拦截器链等信息传入创建一个 CglibMethodInvocation 对象，并调用 Object retVal =  mi.proceed();
* 5）、拦截器链的触发过程;
    * 1)、如果没有拦截器执行执行目标方法，或者拦截器的索引和拦截器数组-1大小一样（指定到了最后一个拦截器）执行目标方法；
    * 2)、链式获取每一个拦截器，拦截器执行invoke方法，每一个拦截器等待下一个拦截器执行完成返回以后再来执行；拦截器链的机制，保证通知方法与目标方法的执行顺序；

总结：

* 1）、  @EnableAspectJAutoProxy 开启AOP功能
* 2）、 @EnableAspectJAutoProxy 会给容器中注册一个组件 AnnotationAwareAspectJAutoProxyCreator
* 3）、AnnotationAwareAspectJAutoProxyCreator是一个后置处理器；
* 4）、容器的创建流程：
    * 1）、registerBeanPostProcessors（）注册后置处理器；创建AnnotationAwareAspectJAutoProxyCreator对象
    * 2）、finishBeanFactoryInitialization（）初始化剩下的单实例bean
        * 1）、创建业务逻辑组件和切面组件
        * 2）、AnnotationAwareAspectJAutoProxyCreator拦截组件的创建过程
        * 3）、组件创建完之后，判断组件是否需要增强
            是：切面的通知方法，包装成增强器（Advisor）;给业务逻辑组件创建一个代理对象（cglib）；
* 5）、执行目标方法：
    * 1）、代理对象执行目标方法
    * 2）、CglibAopProxy.intercept()；
        * 1）、得到目标方法的拦截器链（增强器包装成拦截器MethodInterceptor）
        * 2）、利用拦截器的链式机制，依次进入每一个拦截器进行执行；
        * 3）、效果：
            正常执行：前置通知-》目标方法-》后置通知-》返回通知
            出现异常：前置通知-》目标方法-》后置通知-》异常通知