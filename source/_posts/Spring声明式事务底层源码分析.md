---
title: Spring声明式事务底层源码分析
date: 2019-12-13 13:39:36
update: 2019-12-13 12:39:36
categories: Spring
tags: [transactional]
---

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=280 height=86 src="//music.163.com/outchain/player?type=2&id=186622&auto=0&height=66"></iframe>

<img style="width: 80%;height:80%" src="https://volc1612.gitee.io/blog/images/Spring声明式事务底层源码分析/dog.jpg" />

![spring声明式事务源码](https://volc1612.gitee.io/blog/images/Spring声明式事务底层源码分析/spring声明式事务源码.png)

<!-- more -->


* 1）、@EnableTransactionManagement

	```
	利用TransactionManagementConfigurationSelector给容器中会导入组件
	导入两个组件
	AutoProxyRegistrar组件
	ProxyTransactionManagementConfiguration组件
	```	
 * 2）、AutoProxyRegistrar：

	```
	给容器中注册一个 InfrastructureAdvisorAutoProxyCreator 组件；基本的动态代理创建器
	InfrastructureAdvisorAutoProxyCreator：
	利用后置处理器机制在对象创建以后，包装对象，返回一个代理对象（增强器），代理对象执行方法利用拦截器链进行调用；
	```

 * 3）、ProxyTransactionManagementConfiguration 做了什么？
	* 1）、给容器中注册事务增强器；
		* 1）、事务增强器要用事务注解的信息，AnnotationTransactionAttributeSource解析事务注解
		* 2）、事务拦截器：
			* TransactionInterceptor；保存了事务属性信息，事务管理器；
			* 他是一个 MethodInterceptor；
			* 在目标方法执行的时候；
			* 执行拦截器链；
			* 事务拦截器：
				* 1）、先获取事务相关的属性
				* 2）、再获取PlatformTransactionManager，如果事先没有添加指定任何transactionmanger
					最终会从容器中按照类型获取一个PlatformTransactionManager；
				* 3）、执行目标方法
					如果异常，获取到事务管理器，利用事务管理回滚操作；
					如果正常，利用事务管理器，提交事务

处理事务核心动态代理创建器：
`org.springframework.aop.framework.autoproxy.InfrastructureAdvisorAutoProxyCreator`