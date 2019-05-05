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


使用排它锁测试

```
Thread-31读取商品数据耗时：4401ms
Thread-20读取商品数据耗时：5309ms
Thread-17读取商品数据耗时：6071ms
Thread-30读取商品数据耗时：7827ms
Thread-16读取商品数据耗时：8578ms
Thread-7读取商品数据耗时：9070ms
Thread-0写商品数据耗时：9351ms---------
Thread-3读取商品数据耗时：10272ms
Thread-10读取商品数据耗时：10832ms
Thread-21读取商品数据耗时：11240ms
Thread-18读取商品数据耗时：11896ms
Thread-14读取商品数据耗时：12068ms
Thread-1读取商品数据耗时：12594ms
Thread-9读取商品数据耗时：12648ms
Thread-15读取商品数据耗时：12676ms
Thread-11写商品数据耗时：12762ms---------
Thread-6读取商品数据耗时：13554ms
Thread-25读取商品数据耗时：13690ms
Thread-22写商品数据耗时：14272ms---------
Thread-2读取商品数据耗时：14902ms
Thread-8读取商品数据耗时：15117ms
Thread-27读取商品数据耗时：14981ms
Thread-13读取商品数据耗时：15189ms
Thread-12读取商品数据耗时：15184ms
Thread-26读取商品数据耗时：15191ms
Thread-29读取商品数据耗时：15461ms
Thread-4读取商品数据耗时：15732ms
Thread-24读取商品数据耗时：15572ms
Thread-23读取商品数据耗时：15637ms
Thread-28读取商品数据耗时：15961ms
Thread-19读取商品数据耗时：16169ms
Thread-5读取商品数据耗时：16308ms
Thread-32读取商品数据耗时：16136ms
```

使用读写锁测试

```
Thread-6读取商品数据耗时：730ms
Thread-10读取商品数据耗时：730ms
Thread-2读取商品数据耗时：730ms
Thread-0写商品数据耗时：689ms---------
Thread-3读取商品数据耗时：814ms
Thread-4读取商品数据耗时：815ms
Thread-8读取商品数据耗时：814ms
Thread-7读取商品数据耗时：819ms
Thread-9读取商品数据耗时：819ms
Thread-1读取商品数据耗时：826ms
Thread-5读取商品数据耗时：838ms
Thread-18读取商品数据耗时：775ms
Thread-14读取商品数据耗时：785ms
Thread-11写商品数据耗时：653ms---------
Thread-16读取商品数据耗时：838ms
Thread-12读取商品数据耗时：839ms
Thread-17读取商品数据耗时：841ms
Thread-21读取商品数据耗时：841ms
Thread-19读取商品数据耗时：843ms
Thread-20读取商品数据耗时：843ms
Thread-13读取商品数据耗时：846ms
Thread-15读取商品数据耗时：848ms
Thread-22写商品数据耗时：642ms---------
Thread-32读取商品数据耗时：810ms
Thread-27读取商品数据耗时：795ms
Thread-31读取商品数据耗时：791ms
Thread-28读取商品数据耗时：815ms
Thread-24读取商品数据耗时：816ms
Thread-25读取商品数据耗时：820ms
Thread-30读取商品数据耗时：824ms
Thread-29读取商品数据耗时：825ms
Thread-26读取商品数据耗时：824ms
Thread-23读取商品数据耗时：812ms
```

读写锁`ReentrantReadWriteLock`效率明显要高。在`读多写少`情况下使用读写锁。

#### Condition接口

一个`Lock`可以创建多个`Condition`


显示锁使用`Condition`条件通知

```java
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * 类说明：
 */
public class ExpressCond {
    public final static String CITY = "ShangHai";
    private int km;/*快递运输里程数*/
    private String site;/*快递到达地点*/
    private Lock lock = new ReentrantLock();
    private Condition keCond = lock.newCondition();
    private Condition siteCond = lock.newCondition();

    public ExpressCond(int km, String site) {
        this.km = km;
        this.site = site;
    }

    /* 变化公里数，然后通知处于wait状态并需要处理公里数的线程进行业务处理*/
    public void changeKm() {
        lock.lock();
        try {
            this.km = 101;
            // 唤醒keCond条件等待的线程
            keCond.signalAll();
        } finally {
            lock.unlock();
        }
    }

    /* 变化地点，然后通知处于wait状态并需要处理地点的线程进行业务处理*/
    public void changeSite() {
        lock.lock();
        try {
            // 快递到达目的地后
            this.site = "BeiJing";
            // 唤醒siteCond条件等待的线程
            siteCond.signalAll();
        } finally {
            lock.unlock();
        }
    }

    /*当快递的里程数大于100时更新数据库*/
    public void waitKm() {
        lock.lock();
        try {
            // 小于等于100时一直处于等待状态
            while (this.km <= 100) {
                try {
                    System.out.println("未到达里程数， 线程" + Thread.currentThread().getId() + "处于等待");
                    keCond.await();
                    System.out.println("check km thread[" + Thread.currentThread().getId()
                            + "] is be notifed.");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        } finally {
            lock.unlock();
        }
        System.out.println("the Km is " + this.km + ",I will change db");
    }

    /*当快递到达目的地时通知用户*/
    public void waitSite() {
        lock.lock();
        try {
            // 地点等于出发地上海时一直等待
            while (CITY.equals(this.site)) {
                try {
                    System.out.println("未到达目的地， 线程" + Thread.currentThread().getId() + "处于等待");
                    siteCond.await();
                    System.out.println("check site thread[" + Thread.currentThread().getId()
                            + "] is be notifed.");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        } finally {
            lock.unlock();
        }
        System.out.println("the site is " + this.site + ",I will call user");
    }
}
```

测试

```java
/**
 * 类说明：测试Lock和Condition实现等待通知
 * 快递里程数变化通知
 */
public class TestCond {
    private static ExpressCond express = new ExpressCond(0, ExpressCond.CITY);

    /*检查里程数变化的线程,不满足条件，线程一直等待*/
    private static class CheckKm extends Thread {
        @Override
        public void run() {
            express.waitKm();
        }
    }

    /*检查地点变化的线程,不满足条件，线程一直等待*/
    private static class CheckSite extends Thread {
        @Override
        public void run() {
            express.waitSite();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        // 多个线程通知快递到达目的地
//        for (int i = 0; i < 3; i++) {
//            new CheckSite().start();
//        }
        // 多个线程修改快递里程数
        for (int i = 0; i < 3; i++) {
            new CheckKm().start();
        }
        Thread.sleep(1000);
        express.changeKm();//快递里程变化
//        express.changeSite();//快递到达目的地
    }
}
```

```
未到达里程数， 线程11处于等待
未到达里程数， 线程12处于等待
未到达里程数， 线程13处于等待
check km thread[11] is be notifed.
the Km is 101,I will change db
check km thread[12] is be notifed.
the Km is 101,I will change db
check km thread[13] is be notifed.
the Km is 101,I will change db
```

### AbstractQueuedSynchronizer(AQS)

#### `AQS`使用方式和其中的设计模式

`AQS`使用了继承模板方法设计模式。

##### 模板方法模式

模板方法的父类

```java
import java.util.Date;

/**
 * 模板方法的父类
 */
public abstract class SendCustom {

    public abstract void to();

    public abstract void from();

    public abstract void content();

    public void date() {
        System.out.println(new Date());
    }

    public abstract void send();

    //框架方法-模板方法
    public void sendMessage() {
        to();
        from();
        content();
        date();
        send();
    }
}
```

模板方法的派生类

```java
/**
 * 模板方法的派生类
 */
public class SendSms extends SendCustom {

    @Override
    public void to() {
        System.out.println("Mark");

    }

    @Override
    public void from() {
        System.out.println("Bill");

    }

    @Override
    public void content() {
        System.out.println("Hello world");

    }

    @Override
    public void send() {
        System.out.println("Send sms");
    }

    public static void main(String[] args) {
        SendCustom sendC = new SendSms();
        sendC.sendMessage();
    }
}
```

#### AQS使用的模板方法

> 基本方法 

* 独占式获取锁
accquire
acquireInterruptibly
tryAcquireNanos

* 共享式获取锁
acquireShared
acquireSharedInterruptibly
tryAcquireSharedNanos

* 独占式释放锁
release

* 共享式释放锁
releaseShared

> 需要子类覆盖的流程方法

独占式获取  tryAcquire
独占式释放  tryRelease
共享式获取 tryAcquireShared
共享式释放  tryReleaseShared
这个同步器是否处于独占模式 isHeldExclusively

* 同步状态state：
getState:获取当前的同步状态
setState：设置当前同步状态
compareAndSetState 使用CAS设置状态，保证状态设置的原子性

#### 实现一个类似于`ReentrantLock`的锁 

自己实现的重入锁

```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.AbstractQueuedSynchronizer;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;

/**
 * 类说明：实现一个自己的可重入锁 ReentrantLock
 */
public class SelfLock implements Lock {

    //state 表示获取到锁 state=1 获取到了锁，state=0，表示这个锁当前没有线程拿到
    private static class Sync extends AbstractQueuedSynchronizer {

        /**
         * 判断线程是否占有锁
         */
        protected boolean isHeldExclusively() {
            return getState() == 1;
        }

        /**
         * 尝试获取锁
         *
         * @param arg
         * @return
         */
        protected boolean tryAcquire(int arg) {
            // compareAndSetState原子操作(期望当前没有被线程获取,获取后更新锁已经被线程获取)
            if (compareAndSetState(0, 1)) {
                // 设置当前线程已经拿到了锁
                setExclusiveOwnerThread(Thread.currentThread());
                return true;
            }
            // 尝试获取锁失败
            return false;
        }

        /**
         * 释放锁
         *
         * @param arg
         * @return
         */
        protected boolean tryRelease(int arg) {
            if (getState() == 0) {
                throw new UnsupportedOperationException();
            }
            // 设置没有线程获取到锁
            setExclusiveOwnerThread(null);
            /**
             * 这里设置锁状态不是原子操作，而上面尝试获取锁是原子操作，原因是只有一个线程会释放锁，没有线程抢着释放锁
             */
            setState(0);
            return true;
        }

        /**
         * 新建状态
         *
         * @return
         */
        Condition newCondition() {
            return new ConditionObject();
        }
    }

    private final Sync sycn = new Sync();

    @Override
    public void lock() {
        sycn.acquire(1);

    }

    @Override
    public void lockInterruptibly() throws InterruptedException {
        sycn.acquireInterruptibly(1);

    }

    @Override
    public boolean tryLock() {
        return sycn.tryAcquire(1);
    }

    @Override
    public boolean tryLock(long time, TimeUnit unit) throws InterruptedException {
        return sycn.tryAcquireNanos(1, unit.toNanos(time));
    }

    @Override
    public void unlock() {
        sycn.release(1);

    }

    @Override
    public Condition newCondition() {
        return sycn.newCondition();
    }
}
```

测试锁

```java
import java.util.concurrent.locks.Lock;

/**
 * 类说明：
 */
public class TestMyLock {
    public void test() {
//        final Lock lock = new ReentrantLock();
        final Lock lock = new SelfLock();

        class Worker extends Thread {
            public void run() {
                while (true) {
                    lock.lock();
                    try {
                        SleepTools.second(1);
                        System.out.println(Thread.currentThread().getName());
                        SleepTools.second(1);
                    } finally {
                        lock.unlock();
                    }
                    SleepTools.second(2);
                }
            }
        }
        // 启动10个子线程
        for (int i = 0; i < 10; i++) {
            Worker w = new Worker();
            w.setDaemon(true);
            w.start();
        }
        // 主线程每隔1秒换行
        for (int i = 0; i < 10; i++) {
            SleepTools.second(1);
            System.out.println();
        }
    }

    public static void main(String[] args) {
        TestMyLock testMyLock = new TestMyLock();
        testMyLock.test();
    }
}
```

结果

```java
Thread-0


Thread-1


Thread-0


Thread-3


Thread-7


```

### AQS中的数据结构-节点和同步队列

![显示锁和AQS_a](https://volc1612.gitee.io/blog/images/显示锁和AQS/显示锁和AQS_a.png)

`java.util.concurrent.locks.AbstractQueuedSynchronizer`内部类`Node`属性

* CANCELLED：线程等待超时或者被中断了，需要从队列中移走
* SIGNAL：后续的节点等待状态，当前节点，通知后面的节点去运行
* CONDITION：当前节点处于等待队列
* PROPAGATE：共享中，表示状态要往后面的节点传播
* 初始化状态为`0`

#### 节点加入到同步队列

线程1获取到锁后，线程2无法获取锁就会打包成节点放到`CAS`同步队列中。使用`CAS`操作设置尾节点，因为可能有多个线程同时进行获取锁的操作，都可能成为尾节点。

![显示锁和AQS_b](https://volc1612.gitee.io/blog/images/显示锁和AQS/显示锁和AQS_b.png)

快递添加尾节点源码

```java
private Node addWaiter(Node mode) {
    Node node = new Node(Thread.currentThread(), mode);
    // Try the fast path of enq; backup to full enq on failure
    Node pred = tail;
    if (pred != null) {
        node.prev = pred;
        if (compareAndSetTail(pred, node)) {
            pred.next = node;
            return node;
        }
    }
    // 如果快速添加不成功，就是用CAS自旋添加
    enq(node);
    return node;
}
```    

```java
private Node enq(final Node node) {
    for (;;) {
        Node t = tail;
        if (t == null) { // Must initialize
            if (compareAndSetHead(new Node()))
                tail = head;
        } else {
            node.prev = t;
            if (compareAndSetTail(t, node)) {
                t.next = node;
                return t;
            }
        }
    }
}
```    

#### 首节点的变化

设置首节点不需要`CAS`操作
![显示锁和AQS_c](https://volc1612.gitee.io/blog/images/显示锁和AQS/显示锁和AQS_c.png)

#### 独占式同步状态获取与释放

![显示锁和AQS_d](https://volc1612.gitee.io/blog/images/显示锁和AQS/显示锁和AQS_d.png)

#### 一个Condition包含一个等待队列

![显示锁和AQS_e](https://volc1612.gitee.io/blog/images/显示锁和AQS/显示锁和AQS_e.png)

#### 同步队列与等待队列

![显示锁和AQS_f](https://volc1612.gitee.io/blog/images/显示锁和AQS/显示锁和AQS_f.png)

#### 节点在队列之间的移动

* await方法

![显示锁和AQS_g](https://volc1612.gitee.io/blog/images/显示锁和AQS/显示锁和AQS_g.png)

* signal方法

![显示锁和AQS_h](https://volc1612.gitee.io/blog/images/显示锁和AQS/显示锁和AQS_h.png)


