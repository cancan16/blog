---
title: 原子操作CAS
date: 2019-04-21 12:00:00
update: 2019-04-21 12:00:00
categories: 并发编程
tags: [原子操作CAS]
---

### 原子操作CAS

`synchronized`基于阻塞的锁的机制，存在的问题

* 被阻塞的线程优先级很高
* 拿到锁的线程一直不释放锁怎么办
* 大量的竞争，消耗cpu，同时带来死锁或者其他安全

CAS(Compare And Swap)，指令级别保证这是一个原子操作。
三个运算符： 一个内存地址V，一个期望的值A，一个新值B。
基本思路：如果地址V上的值和期望的值A相等，就给地址V赋给新值B，如果不是，不做任何操作，进入循环（死循环，自旋）里不断的进行CAS操作。

<!-- more -->

### CAS的问题

#### ABA问题

A---》B----》A
解决方案：添加版本号，每次修改A引用指向的内存时，添加版本号，根据引用指向的值和该版本号是否相同，判断其值是否发生过变化。

#### 循环时间长开销大

CAS操作长期不成功，cpu不断的循环

### Jdk中相关原子操作类的使用

* 更新基本类型类：AtomicBoolean，AtomicInteger，AtomicLong
* 更新数组类：AtomicIntegerArray，AtomicLongArray，AtomicReferenceArray
* 更新引用类型：AtomicReference，AtomicMarkableReference，AtomicStampedReference
* 原子更新字段类： AtomicReferenceFieldUpdater，AtomicIntegerFieldUpdater，AtomicLongFieldUpdater

#### AtomicReference

```java
import java.util.concurrent.atomic.AtomicReference;

/**
 * 类说明：演示引用类型的原子操作类
 */
public class UseAtomicReference {

    static AtomicReference<UserInfo> userRef = new AtomicReference<UserInfo>();

    public static void main(String[] args) {
        UserInfo user = new UserInfo("Mark", 15);// 要修改的实体的实例
        userRef.set(user);

        UserInfo updateUser = new UserInfo("Bill", 17);// 要变化的新实例
        // 更新userRef中user信息为updateUser，原子更新，改变的是引用地址
        userRef.compareAndSet(user, updateUser);
        System.out.println(userRef.get().getName());
        System.out.println(userRef.get().getAge());
        System.out.println(user.getName());
        System.out.println(user.getAge());
    }

    //定义一个实体类
    static class UserInfo {
        private String name;
        private int age;

        public UserInfo(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public String getName() {
            return name;
        }

        public int getAge() {
            return age;
        }
    }
}
```

```
Bill
17
Mark
15
```

####

```java
import java.util.concurrent.atomic.AtomicStampedReference;

/**
 * 类说明：演示带版本戳的原子操作类
 */
public class UseAtomicStampedReference {

    static AtomicStampedReference<String> asr =
            new AtomicStampedReference<>("Mark", 0);


    public static void main(String[] args) throws InterruptedException {
        final int oldStamp = asr.getStamp();//那初始的版本号
        final String oldReferenc = asr.getReference();

        System.out.println(oldReferenc + "===========" + oldStamp);

        Thread rightStampThread = new Thread(new Runnable() {

            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()
                        + "当前变量值：" + oldReferenc + "当前版本戳：" + oldStamp + "-"
                        + asr.compareAndSet(oldReferenc, oldReferenc + "Java",
                        oldStamp, oldStamp + 1));

            }

        });

        Thread errorStampThread = new Thread(new Runnable() {

            @Override
            public void run() {
                String reference = asr.getReference();
                System.out.println(Thread.currentThread().getName()
                        + "当前变量值：" + reference + "当前版本戳：" + asr.getStamp() + "-"
                        + asr.compareAndSet(reference, reference + "C",
                        oldStamp, oldStamp + 1));

            }

        });

        rightStampThread.start();
        rightStampThread.join();
        errorStampThread.start();
        errorStampThread.join();
        System.out.println(asr.getReference() + "===========" + asr.getStamp());
    }
}
```

```
Mark===========0
Thread-0当前变量值：Mark当前版本戳：0-true
Thread-1当前变量值：MarkJava当前版本戳：1-false
MarkJava===========1
```