---
title: 显示锁和AQS
date: 2019-04-23 23:35:45
update: 2019-04-23 23:35:45
categories: AQS
tags: [aqs]
---

### 显式锁和AQS

#### 显示锁范式

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * 使用显示锁的范式
 * lock.lock();
 * try {
 * count++;
 * } finally {
 * lock.unlock();
 * }
 */
public class LockDemo {

    // 可重入锁
    private Lock lock = new ReentrantLock();
    private int count;

    /**
     * 显示锁范式
     */
    public void increament() {
        lock.lock();
        try {
            count++;
        } finally {
            lock.unlock();
        }
    }
    public synchronized void incr2() {
        count++;
        incr2();
    }
    public synchronized void test3() {
        incr2();
    }
}
```

#### Lock接口和synchronized的比较

`synchronized`: 代码简洁
`Lock`：获取锁可以被中断，超时获取锁，尝试获取锁。

* 可重入锁：线程可以进入任何一个它已经拥有的锁所同步着的代码块。
* 公平和非公平锁：如果在时间上，先对锁进行获取的请求，一定先被满足，这个锁就是公平的，不满足，就是非公平的。
非公平的效率一般来讲更高。因为线程解除挂起才能获取锁，也需要时间。


* 排他锁： `ReentrantLock`和`Syn`关键字
* 读写锁：同一时刻允许多个读线程同时访问，但是写线程访问的时候，所有的读和写都被阻塞，最适宜与读多写少的情况。

#### 读写锁中`ReentrantReadWriteLock`锁和`synchronized`性能对比

`ReentrantReadWriteLock`实现了`ReadWriteLock`接口。使用商品库存量的场景，模拟`ReentrantReadWriteLock`效率。

商品实体

```java
/**
 * 类说明：商品的实体类
 */
public class GoodsInfo {
    private final String name;
    private double totalMoney;//总销售额
    private int storeNumber;//库存数

    public GoodsInfo(String name, int totalMoney, int storeNumber) {
        this.name = name;
        this.totalMoney = totalMoney;
        this.storeNumber = storeNumber;
    }

    public double getTotalMoney() {
        return totalMoney;
    }

    public int getStoreNumber() {
        return storeNumber;
    }

    public void changeNumber(int sellNumber) {
        this.totalMoney += sellNumber * 25;
        this.storeNumber -= sellNumber;
    }
}
```

功能操作接口

```java
/**
 * 类说明：商品的服务的接口
 */
public interface GoodsService {

    public GoodsInfo getNum();//获得商品的信息

    public void setNum(int number);//设置商品的数量
}
```

使用`synchronized`关键字接口实现

```java
/**
 * 类说明：用内置锁来实现商品服务接口
 */
public class UseSyn implements GoodsService {

    private GoodsInfo goodsInfo;

    public UseSyn(GoodsInfo goodsInfo) {
        this.goodsInfo = goodsInfo;
    }

    @Override
    public synchronized GoodsInfo getNum() {
        // 睡眠
        SleepTools.ms(5);
        return this.goodsInfo;
    }

    @Override
    public synchronized void setNum(int number) {
        SleepTools.ms(5);
        goodsInfo.changeNumber(number);
    }
}
```

使用读写锁`ReentrantReadWriteLock`实现接口功能

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

/**
 * 类说明：
 */
public class UseRwLock implements GoodsService {

    private GoodsInfo goodsInfo;
    private final ReadWriteLock lock = new ReentrantReadWriteLock();
    private final Lock getLock = lock.readLock();//读锁
    private final Lock setLock = lock.writeLock();//写锁

    public UseRwLock(GoodsInfo goodsInfo) {
        this.goodsInfo = goodsInfo;
    }

    @Override
    public GoodsInfo getNum() {
        getLock.lock();
        try {
            SleepTools.ms(5);
            return this.goodsInfo;
        } finally {
            getLock.unlock();
        }

    }

    @Override
    public void setNum(int number) {
        setLock.lock();
        try {
            SleepTools.ms(5);
            goodsInfo.changeNumber(number);
        } finally {
            setLock.unlock();
        }
    }
}
```

性能对比

```java
package com.xiangxue.ch4.rw;

import com.xiangxue.tools.SleepTools;

import java.util.Random;

/**
 * 类说明：对商品进行业务的应用
 */
public class BusiApp {
    static final int readWriteRatio = 10;//读写线程的比例
    static final int minthreadCount = 3;//最少线程数
    //static CountDownLatch latch= new CountDownLatch(1);

    //读操作
    private static class GetThread implements Runnable {

        private GoodsService goodsService;

        public GetThread(GoodsService goodsService) {
            this.goodsService = goodsService;
        }

        @Override
        public void run() {
//            try {
//                latch.await();//让读写线程同时运行
//            } catch (InterruptedException e) {
//            }
            long start = System.currentTimeMillis();
            for (int i = 0; i < 100; i++) {//操作100次
                goodsService.getNum();
            }
            System.out.println(Thread.currentThread().getName() + "读取商品数据耗时："
                    + (System.currentTimeMillis() - start) + "ms");

        }
    }

    //写操做
    private static class SetThread implements Runnable {

        private GoodsService goodsService;

        public SetThread(GoodsService goodsService) {
            this.goodsService = goodsService;
        }

        @Override
        public void run() {
//            try {
//                latch.await();//让读写线程同时运行
//            } catch (InterruptedException e) {
//            }
            long start = System.currentTimeMillis();
            Random r = new Random();
            for (int i = 0; i < 10; i++) {//操作10次
                SleepTools.ms(50);
                goodsService.setNum(r.nextInt(10));
            }
            System.out.println(Thread.currentThread().getName()
                    + "写商品数据耗时：" + (System.currentTimeMillis() - start) + "ms---------");

        }
    }

    public static void main(String[] args) throws InterruptedException {
        GoodsInfo goodsInfo = new GoodsInfo("Cup", 100000, 10000);
        /***使用读写锁测试*/
//        GoodsService goodsService = new UseRwLock(goodsInfo);
        /***使用排它锁测试*/
        GoodsService goodsService = new UseSyn(goodsInfo);
        for (int i = 0; i < minthreadCount; i++) {
            Thread setT = new Thread(new SetThread(goodsService));
            for (int j = 0; j < readWriteRatio; j++) {
                Thread getT = new Thread(new GetThread(goodsService));
                getT.start();
            }
            SleepTools.ms(100);
            setT.start();
        }
        //latch.countDown();

    }
}
```











