---
title: 服务降级熔断Hystrix
date: 2019-05-06 22:02:13
update: 2019-05-06 22:02:13
categories: SPRINGCLOUD
tags: [hystrix]
---

### 熔断

在高并发的情况下，如果请求达到了一定的极限(阈值)，自动开启服务保护功能。通常使用服务降级的方式，熔断服务，为了防止整个系统故障，包含子和下游服务。

### 降级

抛弃一些非核心的接口和数据，在高并发的情况下，请求不能及时响应，防止用户一直等待就可以使用服务降级的处理方式(返回一个友好提示，不会去处理请求，调用fallback本地方法)，目的是为了用户体验。

### 熔断和降级比较

> 相同点

* 从可用性和可靠性触发，为了防止系统崩溃
* 最终让用户体验到的是某些功能暂时不能用

> 不同点

* 服务熔断一般是下游服务故障导致的，而服务降级一般是从整体系统负荷考虑，由调用方控制

### Hystrix使用

<a href="http://cloud.spring.io/spring-cloud-netflix/single/spring-cloud-netflix.html#_circuit_breaker_hystrix_clients">官方文档</a>

> 引入jar

springcloud2.0

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

> 启动类添加注解`@EnableCircuitBreaker`

> api业务层降级处理(`feign`结合`Hystrix`)


订单服务调用商品服务`feignClient`接口。

```java
import com.volc.order_service.controller.fallback.ProductOrderClientFallback;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;

/**
 * 自定义调用商品服务时产生异常处理，(ProductOrderClientFallback类)
 */
@FeignClient(name = "product-service", fallback = ProductOrderClientFallback.class)
public interface ProductFenginService {

    /**
     * 获取商品信息
     *
     * @return
     */
    @GetMapping("/findProductInfo")
    String getProductInfo();
}

```

调用商品服务异常处理类

```java
import com.volc.order_service.controller.service.ProductFenginService;
import org.springframework.stereotype.Component;

/**
 * 针对商品服务做降级处理
 */
@Component
public class ProductOrderClientFallback implements ProductFenginService {
    @Override
    public String getProductInfo() {
        System.out.println("调用商品服务产生异常时处理");
        return "调用商品服务产生异常时处理";
    }
}
```

订单接口调用商品服务出错时，降级处理

```java
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import com.volc.order_service.controller.service.IProductService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * 订单接口
 */
@RestController
@RequestMapping
public class OrderController {

    @Autowired
    private IProductService productService;

    @GetMapping("/getProductInfo")
    @HystrixCommand(fallbackMethod = "getProductInfoFail")
    public String getProductInfo() {
        return productService.getProductInfo();
    }

    /**
     * getProductInfoFail为降级处理方法， 该方法参数类型必须和getProductInfo()的参数类型一致
     *
     * @return
     */
    public String getProductInfoFail() {
        return "业务繁忙";
    }
}
```

### 熔断降级服务异常报警通知

在外层降级处理方法中进行异常报警通知

```java
/**
* getProductInfoFail为降级处理方法， 该方法参数类型必须和getProductInfo()的参数类型一致
* @return
*/
public String getProductInfoFail() {
    //监控报警
    String saveOrderKye = "save-order";
    String sendValue = redisTemplate.opsForValue().get(saveOrderKye);
    final String ip = request.getRemoteAddr();
    new Thread( ()->{
        if (StringUtils.isBlank(sendValue)) {
            System.out.println("紧急短信，用户下单失败，请离开查找原因,ip地址是="+ip);
            //发送一个http请求，调用短信服务 TODO
            redisTemplate.opsForValue().set(saveOrderKye, "save-order-fail", 20, TimeUnit.SECONDS);
        }else{
            System.out.println("已经发送过短信，20秒内不重复发送");
        }
    }).start();
    return "业务繁忙";
}
```

### 源码剖析Hystrix降级策略和调整

> 查看默认策略`HystrixCommandProperties`,`com.netflix.hystrix.HystrixCollapserProperties`

* 隔离策略(用户解决雪崩效应)

execution.isolation.strategy
1.THREAD 线程池隔离 （默认）
2.SEMAPHORE 信号量
信号量适用于接口并发量高的情况，如每秒数千次调用的情况，导致的线程开销过高，通常只适用于非网络调用，执行速度快

* 超时时间`execution.isolation.thread.timeoutInMilliseconds`

默认 1000毫秒

* execution.timeout.enabled 是否开启超时限制 （一定不要禁用）

* execution.isolation.semaphore.maxConcurrentRequests 隔离策略为 信号量的时候，如果达到最大并发数时，后续请求会被拒绝，默认是10
