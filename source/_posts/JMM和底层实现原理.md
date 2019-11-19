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

#### 共享对象对各个线程的可见性

![JMM和底层实现原理-d](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-d.png)

线程1改变主内存中的count，这一动作对于线程2是不可见。线程1没有及时刷新主内存中的count值，时线程2获取的仍然是1

#### 共享对象的竞争现象

![JMM和底层实现原理-e](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-e.png)

#### 重排序问题

![JMM和底层实现原理-f](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-f.jpg)

数据依赖性和控制依赖性不会重排序

> 数据依赖性

![JMM和底层实现原理-g](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-g.png)

> 控制依赖性

``` java
if(a==1){
    // 不会进行重排序，因为需要先判断
    b = 2;
}
```

### 解决在并发下的问题

#### 内存屏障(禁止重排序)

Java编译器在生成指令序列的适当位置会插入内存屏障指令来禁止特定类型的处理器重排序，从而让程序按我们预想的流程去执行。
1、保证特定操作的执行顺序。
2、影响某些数据（或则是某条指令的执行结果）的内存可见性。
编译器和CPU能够重排序指令，保证最终相同的结果，尝试优化性能。插入一条Memory Barrier会告诉编译器和CPU：不管什么指令都不能和这条Memory Barrier指令重排序。
Memory Barrier所做的另外一件事是强制刷出各种CPU cache，如一个Write-Barrier（写入屏障）将刷出所有在Barrier之前写入 cache 的数据，因此，任何CPU上的线程都能读取到这些数据的最新版本。
JMM把内存屏障指令分为4类，解释表格，StoreLoad Barriers是一个“全能型”的屏障，它同时具有其他3个屏障的效果。现代的多处理器大多支持该屏障（其他类型的屏障不一定被所有处理器支持）。

![JMM和底层实现原理-h](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-h.png)

#### 临界区(加锁)

临界区内的代码可以重排序（但JMM不允许临界区内的代码“逸出”到临界区之外，那样会破坏监视器的语义）。JMM会在退出临界区和进入临界区这两个关键时间点做一些特别处理，虽然线程A在临界区内做了重排序，但由于监视器互斥执行的特性，这里的线程B根本无法“观察”到线程A在临界区内的重排序。这种重排序既提高了执行效率，又没有改变程序的执行结果。


![JMM和底层实现原理-i](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-i.png)

### Happens-Before的理解

#### 定义

在Java 规范提案中为让大家理解内存可见性的这个概念，提出了happens-before的概念来阐述操作之间的内存可见性。对应Java程序员来说，理解happens-before是理解JMM的关键。JMM这么做的原因是：程序员对于这两个操作是否真的被重排序并不关心，程序员关心的是程序执行时的语义不能被改变（即执行结果不能被改变）。因此，happens-before关系本质上和as-if-serial语义是一回事。·as-if-serial语义保证单线程内程序的执行结果不被改变，happens-before关系保证正确同步的多线程程序的执行结果不被改变。

用happens-before的概念来阐述操作之间的内存可见性。在JMM中，如果一个操作执行的结果需要对另一个操作可见，那么这两个操作之间必须要存在happens-before关系 。
两个操作之间具有happens-before关系，并不意味着前一个操作必须要在后一个操作之前执行！happens-before仅仅要求前一个操作（执行的结果）对后一个操作可见，且前一个操作按顺序排在第二个操作之前（the first is visible to and ordered before the second）。

##### 加深理解

1）如果一个操作happens-before另一个操作，那么第一个操作的执行结果将对第二个操作可见，而且第一个操作的执行顺序排在第二个操作之前。(对程序员来说)
2）两个操作之间存在happens-before关系，并不意味着Java平台的具体实现必须要按照happens-before关系指定的顺序来执行。如果重排序之后的执行结果，与按happens-before关系来执行的结果一致，那么这种重排序是允许的(对编译器和处理器 来说)。

![JMM和底层实现原理-j](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-j.png)

#### Happens-Before规则

**无需任何同步手段就可以保证的**

1）程序顺序规则：一个线程中的每个操作，happens-before于该线程中的任意后续操作。

2）监视器锁规则：对一个锁的解锁，happens-before于随后对这个锁的加锁。

3）volatile变量规则：对一个volatile域的写，happens-before于任意后续对这个volatile域的读。

4）传递性：如果A happens-before B，且B happens-before C，那么A happens-before C。

5）start()规则：如果线程A执行操作ThreadB.start()（启动线程B），那么A线程的ThreadB.start()操作happens-before于线程B中的任意操作。

6）join()规则：如果线程A执行操作ThreadB.join()并成功返回，那么线程B中的任意操作happens-before于线程A从ThreadB.join()操作成功返回。 

7 ）线程中断规则:对线程interrupt方法的调用happens-before于被中断线程的代码检测到中断事件的发生。


### volatile的内存语义

内存语义：可以简单理解为 volatile，synchronize，atomic，lock 之类的在 JVM 中的内存方面实现原则
可以把对`volatile`变量的单个读/写，看成是使用同一个锁对这些单个读/写操作做了同步。

![JMM和底层实现原理-k](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-k.png)

`i++`并不是线程安全的，即使加上了`volatile`关键字，并不能保证线程安全，因为`i++`在编译后是多行代码执行。

#### volatile变量自身具有下列特性

* 可见性。对一个volatile变量的读，总是能看到（任意线程）对这个volatile变量最后的写入。

* 原子性：对任意单个volatile变量的读/写具有原子性，但类似于volatile++这种复合操作不具有原子性。

volatile写的内存语义如下：当写一个volatile变量时，JMM会把该线程对应的本地内存中的共享变量值刷新到主内存。

```java
public class VolaSemanteme {
	int a = 0;
	volatile boolean flag = false;

	public void init() {
		a = 1; 
		flag = true; 
		//.......
	}

	public void use() {
		if (flag) { 
			int i = a * a; 
		}
		//.......
	}
}
```

![JMM和底层实现原理-l](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-l.png)

对于init方法中`a = 1;`，也会刷新到主内存，因为`a`属于该线程对应的本地内存中的共享变量。

volatile读的内存语义如下：当读一个volatile变量时，JMM会把该线程对应的本地内存置为无效。线程接下来将从主内存中读取共享变量。

![JMM和底层实现原理-m](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-m.png)


#### volatile重排序规则表

![JMM和底层实现原理-n](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-n.png)

####  JMM对volatile的内存屏障插入策略

* 在每个volatile写操作的前面插入一个StoreStore屏障。在每个volatile写操作的后面插入一个StoreLoad屏障。

* 在每个volatile读操作的后面插入一个LoadLoad屏障。在每个volatile读操作的后面插入一个LoadStore屏障。

![JMM和底层实现原理-o](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-o.png)
![JMM和底层实现原理-p](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-p.png)

#### volatile的实现原理

**有volatile变量修饰的共享变量进行写操作的时候会使用CPU提供的Lock前缀指令**

* 将当前处理器缓存行的数据写回到系统内存
* 这个写回内存的操作会使在其他CPU里缓存了该内存地址的数据无效。


### 锁的内存语义


* 当线程释放锁时，JMM会把该线程对应的本地内存中的共享变量刷新到主内存中。
* 当线程获取锁时，JMM会把该线程对应的本地内存置为无效。从而使得被监视器保护的临界区代码必须从主内存中读取共享变量。 

![JMM和底层实现原理-q](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-q.png)
![JMM和底层实现原理-r](https://volc1612.gitee.io/blog/images/JMM和底层实现原理/JMM和底层实现原理-r.png)

### final的内存语义

**编译器和处理器要遵守两个重排序规则**

* 在构造函数内对一个final域的写入，与随后把这个被构造对象的引用赋值给一个引用变量，这两个操作之间不能重排序。
* 初次读一个包含final域的对象的引用，与随后初次读这个final域，这两个操作之间不能重排序。

#### final域为引用类型

增加了如下规则：在构造函数内对一个final引用的对象的成员域的写入，与随后在构造函数外把这个被构造对象的引用赋值给一个引用变量，这两个操作之间不能重排序。

#### final语义在处理器中的实现


* 会要求编译器在final域的写之后，构造函数return之前插入一个StoreStore障屏。
* 读final域的重排序规则要求编译器在读final域的操作前面插入一个LoadLoad屏障。


### synchronized的实现原理


**使用monitorenter和monitorexit指令实现的**

* monitorenter指令是在编译后插入到同步代码块的开始位置，而monitorexit是插入到方法结束处和异常处

* 每个monitorenter必须有对应的monitorexit与之配对 

* 任何对象都有一个monitor与之关联，当且一个monitor被持有后，它将处于锁定状态 

### 其他锁

**锁一共有4种状态，级别从低到高依次是：无锁状态、偏向锁状态、轻量级锁状态和重量级锁状态**

#### 偏向锁

大多数情况下，锁不仅不存在多线程竞争，而且总是由同一线程多次获得，为了让线程获得锁的代价更低而引入了偏向锁。无竞争时不需要进行CAS操作来加锁和解锁。

#### 
