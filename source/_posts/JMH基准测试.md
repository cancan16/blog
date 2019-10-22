---
title: JMH基准测试
date: 2019-10-08 20:45:30
update: 2019-10-08 20:45:30
categories: JMH
tags: [JMH]
---

### 基准测试的意义

* 对业务模型中的重要业务做单独的测试，获取单用户运行时的各项性能指标，为多用户**并发测试**和**综合场景测试**等**性能分析**提供参考依据
* 举个例子 list.contain ==>set.contain ==> 布隆过滤器

![JMH基准测试-a](https://volc1612.gitee.io/blog/images/JMH基准测试/JMH基准测试-a.png)

<!-- more -->

### JMH是什么？
  
JMH，即Java Microbenchmark Harness，这是专门用于进行代码的微基准测试的一套工具API，JMH 由 OpenJDK/Oracle 里面那群开发了 Java 编译器的大牛们所开发

### JMH典型使用场景

* 已经找出了热点函数，而需要对热点函数进行进一步的优化时，就可以使用 JMH 对优化的效果进行定量的分析。
* 想定量地知道某个函数需要执行多长时间，以及执行时间和输入 n 的相关性
* 一个函数有两种不同实现（例如JSON序列化/反序列化有Jackson和Gson实现），不知道哪种实现性能更好


### JMH基准测试的一个helloworld

对比`+=`和`StringBuilder`的性能测试

```java
import org.openjdk.jmh.annotations.Benchmark;
import org.openjdk.jmh.runner.Runner;
import org.openjdk.jmh.runner.RunnerException;
import org.openjdk.jmh.runner.options.Options;
import org.openjdk.jmh.runner.options.OptionsBuilder;

public class JMHHelloWorld {

    public static void main(String[] args) throws RunnerException {
        Options options = new OptionsBuilder().warmupIterations(2).measurementIterations(2)
                .forks(1).build();
        new Runner(options).run();
    }

    @Benchmark
    public void testStringAdd() {
        String s = "";
        for (int i = 0; i < 10; i++) {
            s += i;
        }
    }

    @Benchmark
    public void testStringBuild() {
        StringBuilder b = new StringBuilder();
        for (int i = 0; i < 10; i++) {
            b.append(i);
        }
        b.toString();
    }


}

```

结果：

```
Benchmark                       Mode  Cnt         Score   Error  Units
JMHHelloWorld.testStringAdd    thrpt    2  10987354.301          ops/s
JMHHelloWorld.testStringBuild  thrpt    2  27195429.588          ops/s
```

`testStringAdd`每秒10987354次，`testStringBuild`每秒27195429次，`StringBuild`性能明显大于`+=`。

### SpringBoot整合JMH基准测试


```xml
<dependency>
    <groupId>org.openjdk.jmh</groupId>
    <artifactId>jmh-core</artifactId>
    <version>1.21</version>
</dependency>
<dependency>
    <groupId>org.openjdk.jmh</groupId>
    <artifactId>jmh-generator-annprocess</artifactId>
    <version>1.21</version>
    <scope>provided</scope>
</dependency>
```

```java
import com.xdclass.couponapp.service.CouponService;
import org.openjdk.jmh.annotations.*;
import org.openjdk.jmh.runner.Runner;
import org.openjdk.jmh.runner.RunnerException;
import org.openjdk.jmh.runner.options.Options;
import org.openjdk.jmh.runner.options.OptionsBuilder;
import org.springframework.boot.SpringApplication;
import org.springframework.context.ConfigurableApplicationContext;


/**
 * @Author: Liu, cancan
 * @Description: springboot整合JMH基准测试，测试接口性能
 * @Date: 2019/10/9
 */
@State(Scope.Thread)
public class JMHSpringbootTest {
    private ConfigurableApplicationContext context;
    private CouponService couponService;
    public static void main(String[] args) throws RunnerException {
        /**
         * 指定`JMHSpringbootTest`类执行Options操作
         */
        Options options = new OptionsBuilder().include(JMHSpringbootTest.class.getName() + ".*")
                .warmupIterations(2).measurementIterations(2)
                .forks(1).build();
        new Runner(options).run();
    }

    /**
     * setup初始化容器的时候只执行一次
     */
    @Setup(Level.Trial)
    public void init() {
        String arg = "";
        context = SpringApplication.run(CouponAppApplication.class, arg);
        couponService = context.getBean(CouponService.class);
    }


    /**
     * benchmark执行多次，此注解代表触发我们所要进行基准测试的方法
     */
    @Benchmark
    public void test() {
        System.out.println(couponService.getCouponList());
    }

}

```

### 分析DB查询，loadingCache,currentHashMap性能对比

* 从数据库中获取数据

```java
public List<TCoupon> loadCoupon(Integer o) {
    TCouponExample example = new TCouponExample();
    example.createCriteria().andStatusEqualTo(Constant.USERFUL)
            .andStartTimeLessThan(new Date()).andEndTimeGreaterThan(new Date());
    return tCouponMapper.selectByExample(example);
}
```

* 从`LoadingCache`本地缓存中获取书


```java
LoadingCache<Integer, List<TCoupon>> couponCache = CacheBuilder.newBuilder()
            // 过期时间
            .expireAfterWrite(10, TimeUnit.MINUTES)
            // 异步刷新时间间隔5秒，往本地缓存loadingcache中写完后，5s后秒刷新缓存
            .refreshAfterWrite(5, TimeUnit.MINUTES)
            .build(new CacheLoader<Integer, List<TCoupon>>() {
                @Override
                public List<TCoupon> load(Integer o) throws Exception {
                    System.out.println("每5秒刷新列表到本地缓存");
                    return loadCoupon(o);
                }
            });

public List<TCoupon> getCouponList() {
    List<TCoupon> tCoupons = Lists.newArrayList();
    try {
        tCoupons = couponCache.get(1);
    } catch (ExecutionException e) {
        e.printStackTrace();
    }
    return tCoupons;
}

public List<TCoupon> loadCoupon(Integer o) {
    TCouponExample example = new TCouponExample();
    example.createCriteria().andStatusEqualTo(Constant.USERFUL)
            .andStartTimeLessThan(new Date()).andEndTimeGreaterThan(new Date());
    return tCouponMapper.selectByExample(example);
}
```

* 从`currentHashMap`中拿数据

```java
/***
    * 获取有效时间的可用优惠券列表
    * @return
    */
public List<TCoupon> getCouponList4Map() {
    return (List<TCoupon>) couponMap.get(1);
}
```

#### 测试结果

```
Benchmark                   Mode  Cnt       Score   Error  Units
JMHSpringbootTest.test     thrpt    2   94010.347          ops/s
JMHSpringbootTest.testDB   thrpt    2    5699.791          ops/s
JMHSpringbootTest.testMap  thrpt    2  103801.969          ops/s
```

结果来看从`currentHashMap`获取数据性能最高，每秒可达到103801次