---
title: SpringMVC与Servlet3.0
date: 2019-12-23 12:39:36
update: 2019-12-23 12:39:36
categories: Spring
tags: [mvc,servlet]
---

<div style="text-align: center"><iframe height="60" width="260" src="https://www.ximalaya.com/thirdparty/player/sound/player.html?id=6549547&type=red" frameborder=0 allowfullscreen></iframe></div>

<img style="width: 80%;height:80%" src="https://volc1612.gitee.io/blog/images/Spring组件及AOP底层源码/dog.jpeg" />

<!-- more -->

### 原生servlet处理请求

* servlet处理器类

```java
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/order")
public class JamesServlet extends HttpServlet {

    //重写doget方法
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println(Thread.currentThread() + "start.....");
        try {
            buyCards();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        resp.getWriter().write("order sucesful....");
        System.out.println(Thread.currentThread() + " end..........");
    }

    public void buyCards() throws InterruptedException {
        System.out.println(Thread.currentThread() + ".............");
        Thread.sleep(5000);//模拟业务操作
    }
}
```

* 项目放到tomcat容器中启动

* 浏览器访问`http://localhost:8080/项目名称/order`查看结果

### ServletContainerInitializer初始化web容器

* 使用IDEA创建一个java Enterprise项目

JDK：1.8
JAVA EE vesion：java EE8
Application server：tomcat7
Additional Libraries and Frameworks: web application

![demo结构](https://volc1612.gitee.io/blog/images/springmvc与servlet3.0/demo结构.png)

* 由`servlet`官方提供文档可知，初始化`web`容器需要创建`javax.servlet.ServletContainerInitializer`文件

* 创建`javax.servlet.ServletContainerInitializer`文件

路径如下：`META-INF/services/javax.servlet.ServletContainerInitializer`
内容：容器路径
```
com.enjoy.sevlet.JamesServletContainerInitializer
```

* 容器实现`ServletContainerInitializer`接口

1. 通过`@HandlesTypes`可以将感兴趣的一些类注入到`ServletContainerInitializer`的`onStartup`方法作为参数传入自己的类，以及该类的子类和实现类，并可以通过反射创建对象；
2. 注入`Servlet`，`Filter`，`listener`三大组件；

* 创建`servlet`

```java
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class OrderServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("OrderServlet...............");
    }
}

```

* 创建过滤器

```java
import javax.servlet.*;
import java.io.IOException;

public class OrderFilter implements Filter {

    @Override
    public void destroy() {
        // TODO Auto-generated method stub

    }

    @Override
    public void doFilter(ServletRequest arg0, ServletResponse arg1, FilterChain arg2)
            throws IOException, ServletException {
        //过滤请求
        System.out.println("OrderFilter....doFilter.....");
        //放行
        arg2.doFilter(arg0, arg1);
    }

    @Override
    public void init(FilterConfig arg0) throws ServletException {
        // TODO Auto-generated method stub
    }
}
```

* 创建监听器

```java
import javax.servlet.ServletContext;
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
//监听项目的启动或停止
public class OrderListener implements ServletContextListener {
    @Override
    public void contextDestroyed(ServletContextEvent arg0) {
        // TODO Auto-generated method stub
        System.out.println("OrderListener...contextDestroyed");
    }
    @Override
    public void contextInitialized(ServletContextEvent arg0) {
        // TODO Auto-generated method stub
        ServletContext servletContext = arg0.getServletContext();
        System.out.println("OrderListener...contextInitialized");
    }
}
```

* 创建`web`容器

```java
import com.enjoy.others.OrderFilter;
import com.enjoy.others.OrderListener;
import com.enjoy.others.OrderServlet;

import javax.servlet.DispatcherType;
import javax.servlet.ServletContainerInitializer;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.HandlesTypes;
import java.util.EnumSet;
import java.util.Set;

@HandlesTypes(value = {JamesService.class})
public class JamesServletContainerInitializer implements ServletContainerInitializer {
    //arg0:父类感兴趣的类的子类型
    //ServletContext arg1:....代表当前web应用,注册3组件
    @Override
    public void onStartup(Set<Class<?>> arg0, ServletContext arg1) throws ServletException {

        System.out.println("感兴趣的类型");
        for (Class<?> claz : arg0) {
            System.out.println(claz);// 反射
        }
        //注册OrderServlet组件
        javax.servlet.ServletRegistration.Dynamic servlet = arg1.addServlet("orderServlet", new OrderServlet());
        servlet.addMapping("/orderTest");

        //注册监听器listener
        arg1.addListener(OrderListener.class);

        //注册filter
        javax.servlet.FilterRegistration.Dynamic filter = arg1.addFilter("orderFilter", OrderFilter.class);
        //添加filer的映射信息，可以指定专门来拦截哪个servlet
        filter.addMappingForUrlPatterns(EnumSet.of(DispatcherType.REQUEST), true, "/*");

    }
}
```

**在运行的过程中，是不可以注册组件， 和IOC道理一样，出于安全考虑**
**基于运行时插件的机制，启动并运行这个`ServletContainerInitializer`，在整合`springmvc`的时候会用到**


### 使用注解的方式（配置类）来完成配置springmvc的整合

![Servlet调度器](https://volc1612.gitee.io/blog/images/springmvc与servlet3.0/springmvc.png)

* root根容器与servlet容器的区别在哪呢

servlet的容器用来处理@Controller，视图解析，和web相关组件而root根容器主要针对服务层，和数据源DAO层及事务控制相关处理

#### 配置类整合springmvc

* 引入依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.0.6.RELEASE</version>
    </dependency>

    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>3.0-alpha-1</version>
        <scope>provided</scope>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>2.4</version>
            <configuration>
                <failOnMissingWebXml>false</failOnMissingWebXml>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>6</source>
                <target>6</target>
            </configuration>
        </plugin>
    </plugins>
</build>
```

* 子类`AbstractAnnotationConfigDispatcherServletInitializer`

由源码`org.springframework.web.context.WebApplicationContext`接口的实现类可知：

`AbstractDispatcherServletInitializer`的作用：从名字来看可知是`DispatcherServlet`初始化；
`AbstractAnnotationConfigDispatcherServletInitializer`：注解方式配置的`dispatcherServlet`初始化器；

* 新建`JamesWebInitializer`继承`AbstractAnnotationConfigDispatcherServletInitializer`类

```java
import com.enjoy.config.JamesAppConfig;
import com.enjoy.config.JamesRootConfig;
import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;


//web容器启动的时候创建对象；调用方法来初始化容器以前前端控制器
public class JamesWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    //获取根容器的配置类；（Spring的配置文件）   父容器；
    @Override
    protected Class<?>[] getRootConfigClasses() {
        //指定配置类（配置文件）位置
        return new Class<?>[]{JamesRootConfig.class};
    }

    //获取web容器的配置类（SpringMVC配置文件）  子容器；
    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class<?>[]{JamesAppConfig.class};
    }

    //获取DispatcherServlet的映射信息
    //  /：拦截所有请求（包括静态资源（xx.js,xx.png）），但是不包括*.jsp；
    //  /*：拦截所有请求；连*.jsp页面都拦截；jsp页面是tomcat的jsp引擎解析的；
    @Override
    protected String[] getServletMappings() {
        // TODO Auto-generated method stub
        return new String[]{"/"};
    }

}
```

* 拦截器

```java
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class JamesInterceptor implements HandlerInterceptor {
    //在目标方法运行之间执行
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {
        System.out.println(Thread.currentThread() + "----preHandle-------------" + request.getRequestURI());
        return true;
    }

    //在目标方法执行之后执行
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
                           ModelAndView modelAndView) throws Exception {
        System.out.println(Thread.currentThread() + "----postHandle-------------" + request.getRequestURI());
    }

    //页面响应之后执行
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
            throws Exception {
        System.out.println(Thread.currentThread() + "----afterCompletion-------------" + request.getRequestURI());
    }
}
```

* 新建两个配置类`JamesRootConfig`和`JamesAppConfig`，形成父子容器的效果

```java
import com.enjoy.controller.JamesInterceptor;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.ComponentScan.Filter;
import org.springframework.context.annotation.FilterType;
import org.springframework.stereotype.Controller;
import org.springframework.web.servlet.config.annotation.*;


//SpringMVC只扫描Controller；子容器
//useDefaultFilters=false 禁用默认的过滤规则；
@ComponentScan(value = "com.enjoy", includeFilters = {
        @Filter(type = FilterType.ANNOTATION, classes = {Controller.class})
}, useDefaultFilters = false)
@EnableWebMvc
public class JamesAppConfig extends WebMvcConfigurerAdapter {

    //定制视图解析器
    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        //比如我们想用JSP解析器,默认所有的页面都从/WEB-INF/AAA.jsp
        registry.jsp("/WEB-INF/pages/", ".jsp");
    }

    //静态资源访问,静态资源交给tomcat来处理
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }

    //拦截器
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new JamesInterceptor()).addPathPatterns("/**");
    }
}
```

```java
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.ComponentScan.Filter;
import org.springframework.context.annotation.FilterType;
import org.springframework.stereotype.Controller;
//Spring的容器不扫描controller;父容器
@ComponentScan(value = "com.enjoy", excludeFilters = {
        @Filter(type = FilterType.ANNOTATION, classes = {Controller.class})
})
public class JamesRootConfig {
}
```

* `OrderController`控制类来测试

```java
import com.enjoy.service.OrderService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class OrderController {
    @Autowired
    OrderService orderService;

    @ResponseBody
    @RequestMapping("/buy")
    public String buy() {
        return orderService.goBuy("12345678");
    }

    //相当于会找 /WEB-INF/pages/ok.jsp
    @RequestMapping("/ok")
    public String ok() {
        return "ok";
    }
}
```

* 重启`tomcat`，进行测试`http://localhost:8080/springmvcanno/buy`

### servlet3.0异步请求分析

* 创建一个`servlet`支持异步处理

```java
import javax.servlet.AsyncContext;
import javax.servlet.ServletException;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(value = "/asyncOrder", asyncSupported = true)
public class OrderAsyncServlet extends HttpServlet {
    //支持异步处理asyncSupported = true
    //重写doget方法
    @Override
    protected void doGet(final HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //151个……
        System.out.println("主线程开始……" + Thread.currentThread() + "start....." + System.currentTimeMillis());
        final AsyncContext startAsync = req.startAsync();

        startAsync.start(new Runnable() {
            @Override
            public void run() {
                try {
                    System.out.println("副线程开始……" + Thread.currentThread() + "start....." + System.currentTimeMillis());

                    buyCards();
                    startAsync.complete();
                    AsyncContext asyncContext = req.getAsyncContext();
                    ServletResponse response = asyncContext.getResponse();
                    response.getWriter().write("order sucesful....");
                    System.out.println("副线程结束……" + Thread.currentThread() + "end....." + System.currentTimeMillis());

                } catch (InterruptedException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                } catch (IOException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            }
        });

        System.out.println("主线程结束……" + Thread.currentThread() + "end....." + System.currentTimeMillis());
        //主线程的资源断开……
    }

    public void buyCards() throws InterruptedException {
        System.out.println(Thread.currentThread() + ".............");
        Thread.sleep(5000);//模拟业务操作
    }
}
```

```
主线程开始……Thread[http-bio-8080-exec-7,5,main]start.....1577263536958
主线程结束……Thread[http-bio-8080-exec-7,5,main]end.....1577263536965
副线程开始……Thread[http-bio-8080-exec-9,5,main]start.....1577263536965
Thread[http-bio-8080-exec-9,5,main].............
```

**总结**

* @WebServlet注解
* AsyncContext开启异步
* 不管是同步`servlet`处理请求还是异步`servlet`处理请求，浏览器都会等待异步处理完成后才能响应，并不会因为主线程结束才会响应给浏览器。


#### servlet异步处理原理

![异步处理原理](https://volc1612.gitee.io/blog/images/springmvc与servlet3.0/异步处理原理.png)

* 有图可见`servlet`响应是子线程相关的，和`tomcat`主线程并无关系
* `tomcat`主线线程接收请求，并把处理方式交给子线程


### springmvc异步处理

```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.concurrent.Callable;


@Controller
public class AsyncOrderController {

//    //其实相当于我们说的tomcat的线程1，来处理用户请求，并将请求的操作放到Queue队列里
//    @ResponseBody
//    @RequestMapping("/createOrder")
//    public DeferredResult<Object> createOrder() {
//
//
//        DeferredResult<Object> deferredResult = new DeferredResult<>((long) 10000, "create fail...");
//
//        JamesDeferredQueue.save(deferredResult);
//
//        return deferredResult;
//    }
//
//    ////其实相当于我们说的tomcat的线程N，来处理用户请求，并将请求的操作放到Queue队列里
//    @ResponseBody
//    @RequestMapping("/create")
//    public String create() {
//        //创建订单（按真实操作应该是从订单服务取，这里直接返回）
//        String order = UUID.randomUUID().toString();//模拟从订单服务获取的订单信息（免调接口）
//        DeferredResult<Object> deferredResult = JamesDeferredQueue.get();
//        deferredResult.setResult(order);
//        return "create success, orderId == " + order;
//    }

    @ResponseBody
    @RequestMapping("/order01")
    public Callable<String> order01() {
        System.out.println("主线程开始..." + Thread.currentThread() + "==>" + System.currentTimeMillis());

        Callable<String> callable = new Callable<String>() {
            @Override
            public String call() throws Exception {
                System.out.println("副线程开始..." + Thread.currentThread() + "==>" + System.currentTimeMillis());
                Thread.sleep(2000);
                System.out.println("副线程开始..." + Thread.currentThread() + "==>" + System.currentTimeMillis());
                return "order buy successful........";
            }
        };
        System.out.println("主线程结束..." + Thread.currentThread() + "==>" + System.currentTimeMillis());
        return callable;
    }
}
```

结果浏览器停止2秒返回`order buy successful........`，并在控制台打印出结果

```
Thread[http-bio-8080-exec-8,5,main]----preHandle-------------/springmvc_anno_war_exploded/order01
主线程开始...Thread[http-bio-8080-exec-8,5,main]==>1577676355466
主线程结束...Thread[http-bio-8080-exec-8,5,main]==>1577676355466
副线程开始...Thread[MvcAsync2,5,main]==>1577676355469
副线程开始...Thread[MvcAsync2,5,main]==>1577676357469
Thread[http-bio-8080-exec-10,5,main]----preHandle-------------/springmvc_anno_war_exploded/order01
Thread[http-bio-8080-exec-10,5,main]----postHandle-------------/springmvc_anno_war_exploded/order01
Thread[http-bio-8080-exec-10,5,main]----afterCompletion-------------/springmvc_anno_war_exploded/order01
```

`springmvc`官网文档： `https://docs.spring.io/spring/docs/5.0.0.RELEASE/spring-framework-reference/web.html#mvc` `1.3.4`说明

由此可见，`springmvc`的异步处理过程

* `springmvc`接收请求把请求交给`Servlet`容器处理，并异步处理业务`Callable`生成一个结果，但是结果并未响应；
* `springmvc`再次把请求转发给`servlet`容器，并获取`Callable`结果，响应给浏览器；
* `The Callable produces a result and Spring MVC dispatches the request back to the Servlet container to resume processing`（`Callable`生成一个结果，`Spring MVC`将请求发送回`Servlet`容器以恢复处理）

#### 模拟异步处理业务

![异步图解](https://volc1612.gitee.io/blog/images/springmvc与servlet3.0/异步图解.png)

* 一个线程接口请求一个线程返回请求

```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.context.request.async.DeferredResult;

import java.util.UUID;
import java.util.concurrent.Callable;


@Controller
public class AsyncOrderController {

    /**
     * 模拟接收请求的线程
     * 10000： 队列元素生存10秒
     * 1. 其实相当于我们说的tomcat的线程1，来处理用户请求，并将请求的操作放到Queue队列里，
     * 2. 当队列中的deferredResult有值时，并且从deferredResult放入队列到deferredResult有值，没有超过10秒时，会立即返回deferredResult的值
     * 可以理解为`deferredResult`一直在阻塞，监听有没有设置值，如果有设置值就返回，没有就等待，直到超时
     * 3. 当deferredResult没有值或超过了队列元素的生存时间，则会返回create fail...
     */
    @ResponseBody
    @RequestMapping("/createOrder")
    public DeferredResult<Object> createOrder() {
        DeferredResult<Object> deferredResult = new DeferredResult<Object>((long) 10000, "create fail...");
        JamesDeferredQueue.save(deferredResult);
        return deferredResult;
    }

    /**
     * 模拟新的线程进行处理业务响应的值
     *
     * @return
     */
    @ResponseBody
    @RequestMapping("/get")
    public String get() {
        //创建订单（按真实操作应该是从订单服务取，这里直接返回）
        String order = UUID.randomUUID().toString();//模拟从订单服务获取的订单信息（免调接口）
        DeferredResult<Object> deferredResult = JamesDeferredQueue.get();
        deferredResult.setResult(order);
        return order;
    }
}
```



