---
title: JMM和底层实现原理
date: 2019-07-31 16:35:45
update: 2019-07-31 16:35:45
categories: 并发编程
tags: [JMM]
---

### Java内存模型（JMM）

即Java Memory Model，简称JMM。JMM定义了Java 虚拟机(JVM)在计算机内存(RAM)中的工作方式。JVM是整个计算机虚拟模型，所以JMM是隶属于JVM的。从抽象的角度来看，JMM定义了线程和主内存之间的抽象关系：线程之间的共享变量存储在主内存（Main Memory）中，每个线程都有一个私有的本地内存（Local Memory），本地内存中存储了该线程以读/写共享变量的副本。本地内存是JMM的一个抽象概念，并不真实存在。它涵盖了缓存、写缓冲区、寄存器以及其他的硬件和编译器优化。

![JMM和底层实现原理-a](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-a.jpg)
![JMM和底层实现原理-b](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-b.png)

### 多线程共享堆中的对象

![JMM和底层实现原理-c](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-c.png)


线程栈还包含了当前方法的所有局部变量信息。一个线程只能读取自己的线程栈，也就是说，线程中的本地变量对其它线程是不可见的。即使两个线程执行的是同一段代码，它们也会各自在自己的线程栈中创建局部变量，因此，每个线程中的局部变量都会有自己的版本

### java内存模型带来的问题

1. 共享对象对各个线程的可见性
2. 共享对象的竞争现象

![JMM和底层实现原理-d](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-d.png)

线程1改变主内存中的count，这一动作对于线程2是不可见。线程1没有及时刷新主内存中的count值，时线程2获取的仍然是1

![JMM和底层实现原理-e](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-e.png)









