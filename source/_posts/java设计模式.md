---
title: java设计模式
date: 2016-11-24 10:57:13
update: 2016-11-24 10:57:13
categories: JAVA
tags: [java, 设计模式]
---

# singleton单例模式

<!-- more -->

```java
package myprogram;
/**
 * @ClassName Singleton
 * @Description 单例模式-懒汉式
 * 既实现了线程安全，又避免了同步带来的性能影响
 * @Author youtanzhi
 * @Date 2016年11月15日 下午3:58:33
 */
public class Singleton {
    private static Singleton instance;
    private Singleton (){} // 只有当前类可以使用
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```
```java
package myprogram;
/**
 * @ClassName Singleton1
 * @Description 饿汉式单例类.在类初始化时，已经自行实例化
 * @Author youtanzhi
 * @Date 2016年11月15日 下午4:02:31
 */
public class Singleton1 {
    private Singleton1() {
    }

    private static final Singleton1 single = new Singleton1();

    // 静态工厂方法
    public static Singleton1 getInstance() {
	return single;
    }
}
```
