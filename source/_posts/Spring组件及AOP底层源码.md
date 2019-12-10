---
title: Spring组件及AOP底层源码
date: 2019-12-10 12:39:36
update: 2019-12-10 12:39:36
categories: Spring
tags: [aop]
---

<div style="text-align: center"><iframe height="230" width="260" src="https://www.ximalaya.com/thirdparty/player/sound/player.html?id=173837050&type=red" frameborder=0 allowfullscreen></iframe></div>

<img style="width: 60%;height:60%" src="https://volc1612.gitee.io/blog/images/Spring组件及AOP底层源码/dog.jpeg" />

<!-- more -->

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