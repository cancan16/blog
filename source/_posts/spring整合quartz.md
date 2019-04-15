---
title: spring整合quartz
date: 2017-04-09 12:29:23
update: 2017-06-02 12:29:23
categories: JAVA
tags: [quartz]
---
### 更新
<!-- more -->
#### 问题
##### 问题1 quartz解决并发问题
要清楚一点是，synchronized同步是不能够完全处理并发问题的。
产生原因：触发时间间隔小于程序运行时间间隔，也就是当前任务没有结束就进行了下一轮任务。
这样容易导致数据混乱，是不可行的。

applicationContext-schedule.xml配置文件
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd"
	default-lazy-init="true">
	<!-- 线程池执行器配置，用于任务注册 -->
	<bean id="executor" class="org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor">
		<property name="corePoolSize" value="1" />
		<property name="maxPoolSize" value="1" />
		<property name="queueCapacity" value="500" />
	</bean>
	<bean id="testJobTask" class="cn.volc.task.job.TestJobTask" />

	<!-- 定义任务bean -->
	<bean name="myJobDetail" class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
		<property name="targetObject" ref="testJobTask" />
		<property name="targetMethod" value="excute" />
		<!-- false:定义这个任务不能并发执行(很关键) -->
		<property name="concurrent" value="false" />
	</bean>

	<!-- 定义触发器 -->
	<bean id="cronTrigger" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
		<property name="jobDetail" ref="myJobDetail" />
		<!-- 每半小时执行一次 (0/30000 * * * * ?)(凌晨一点)0 0 1 * * ? | 0 5 16 17 5 ? -->
		<property name="cronExpression" value="0/1 * * * * ?" />
	</bean>

	<!-- 定义调度器 -->
	<bean class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
		<property name="triggers">
			<list>
				<ref bean="cronTrigger" />
				<!-- <ref bean="cronTrigger2" /> -->
			</list>
		</property>
		<property name="taskExecutor" ref="executor" />
	</bean>
</beans>
```
TestJobTask任务处理类
```java

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class TestJobTask {
    public void excute() {
	System.out.println("Start job");
	// 创建线程池大小
	ExecutorService exec = Executors.newFixedThreadPool(3);
	for (int i = 0; i < 2; i++) {
	    exec.execute(new TestThread());
	}
	exec.shutdown();
	while (!exec.isTerminated()) {
	}
	System.out.println("end job");
    }

}

```
TestThread类
```java
package cn.volc.task.job;

public class TestThread implements Runnable {

    @Override
    public void run() {
	try {
	    System.out.println("执行线程");
	    Thread.sleep(5000);
	    System.out.println("执行完毕");
	} catch (InterruptedException e1) {
	    e1.printStackTrace();
	}
    }
}
```
虽然轮询间隔为1秒，但是上一轮询主线程中的子线程(睡眠5秒)没结束，主线程不会继续往下执行，就不会进入下一轮询。处理并发问题可采用这种方案。

##### 问题2 多线程保证数据准确性(待优化)

问题产生原因
在次线程1中创建一个对象，并实例化其中的static变量值，那么次线程2也实例化了该对象并更新了其static变量值，
在次线程1中使用实例化的对象时数据是不准确定，导致数据产生混乱。
解决方案：以传递参数的方式，传递实例化的对象中的方法所需要的参数，而不是在实例化对象时赋予变量值。
强烈参照：<a href="http://blog.csdn.net/mark_lq/article/details/50329559">多线程保证数据的隔离性</a>

### 1 quartz使用
&emsp;&emsp;**Quartz**是一个Java下作业控制的开源框架。Quartz用来创建或简单或复杂的调度时间表，执行Java下任意数量的作业。版本1.0发布于2002年9月13日，当前版本2.2.1发布于2013年9月24日。
可以通过CronTrigger定义Quartz的调度时间表（例如0 0 12 ? * WED表示“每周三上午12：00”）。此外，时间表也可以通过SimpleTrigger，由Date定义触发的开始时间、毫秒的时间间隔和重复计数（例如“在下周三12：00，然后每隔10秒、执行5次”）。可以使用Calender定义例外的日程（例如“没有周末和节假日”）。
作业可以是实现了Job接口任意的Java类。作业监听器（JobListener）和触发器监听器（TriggerListener）通知作业的执行（和其他事件）。作业及其触发器可以被持久化。
Quartz一般用于企业级应用程序，以支持工作流、系统管理（维护）活动，并在应用程序中提供实时的服务。Quartz还支持集群。
Quartz是Terracotta公司的开源产品。 .NET平台下的对应产品叫Quartz.NET。
quratz任务的注册(蓝线)和执行(红线)
[^_^]:![](http://localhost:4000/images/spring整合quratz/quratz.png)
![quartz](http://volc1612.gitee.io/blog/images/spring整合quratz/quratz.PNG)
quartz核心接口
>* Schecduler  核心调度器
>* Job 		任务
>* JobDetail   任务描述
>* Trigger     触发器

Tigger
>* SimpleTrigger 简单的触发，指定时间
>* CronTrigger 表达式触发,处理复杂时间执行，类似日历

### 2 简单使用demo
定义job
```java
import java.util.Date;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.quartz.Job;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;

/**
 * <p>
 * This is just a simple job that says "Hello" to the world.
 * </p>
 * 
 * @author Bill Kratzer
 */
public class HelloJob implements Job {

    private static Logger _log = LoggerFactory.getLogger(HelloJob.class);

    /**
     * <p>
     * Empty constructor for job initilization
     * </p>
     * <p>
     * Quartz requires a public empty constructor so that the
     * scheduler can instantiate the class whenever it needs.
     * </p>
     */
    public HelloJob() {
    }

    /**
     * <p>
     * Called by the <code>{@link org.quartz.Scheduler}</code> when a
     * <code>{@link org.quartz.Trigger}</code> fires that is associated with
     * the <code>Job</code>.
     * </p>
     * 
     * @throws JobExecutionException
     *             if there is an exception while executing the job.
     */
    public void execute(JobExecutionContext context) throws JobExecutionException {

	// Say Hello to the World and display the date/time
	_log.info("Hello World! - " + new Date());
    }

}
```
简单注册和触发demo
```java
import static org.quartz.DateBuilder.evenMinuteDate;
import static org.quartz.JobBuilder.newJob;
import static org.quartz.TriggerBuilder.newTrigger;
import org.quartz.JobDetail;
import org.quartz.Scheduler;
import org.quartz.SchedulerFactory;
import org.quartz.Trigger;
import org.quartz.impl.StdSchedulerFactory;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import java.util.Date;

/**
 * This Example will demonstrate how to start and shutdown the Quartz scheduler and how to schedule
 * a job to run in Quartz.
 * 
 * @author Bill Kratzer
 */
public class SimpleExample {

    public void run() throws Exception {
	Logger log = LoggerFactory.getLogger(SimpleExample.class);

	log.info("------- Initializing ----------------------");

	// 定义调度器
	SchedulerFactory sf = new StdSchedulerFactory();
	Scheduler sched = sf.getScheduler();

	log.info("------- Initialization Complete -----------");

	// 获取当前时间的下一分钟
	Date runTime = evenMinuteDate(new Date());

	log.info("------- Scheduling Job  -------------------");

	// 定义job,要执行的HelloJob,组曾jobdetail
	// 在quartz中，有组的概念，组+job名称 唯一的
	JobDetail job = newJob(HelloJob.class).withIdentity("job1", "group1").build();

	// 定义触发器，在下一分钟启动
	Trigger trigger = newTrigger().withIdentity("trigger1", "group1").startAt(runTime).build();

	// 将job注册到调度器
	sched.scheduleJob(job, trigger);
	log.info(job.getKey() + " will run at: " + runTime);

	// 启动调度器
	sched.start();

	log.info("------- Started Scheduler -----------------");

	// 等待65秒
	log.info("------- Waiting 65 seconds... -------------");
	try {
	    // wait 65 seconds to show job
	    Thread.sleep(65L * 1000L);
	    // executing...
	} catch (Exception e) {
	    //
	}

	// 关闭调度器
	log.info("------- Shutting Down ---------------------");
	sched.shutdown(true);
	log.info("------- Shutdown Complete -----------------");
    }

    public static void main(String[] args) throws Exception {

	SimpleExample example = new SimpleExample();
	example.run();

    }

}
```
表达式注册和触发demo
```java
import static org.quartz.CronScheduleBuilder.cronSchedule;
import static org.quartz.DateBuilder.evenMinuteDate;
import static org.quartz.JobBuilder.newJob;
import static org.quartz.TriggerBuilder.newTrigger;
import org.quartz.JobDetail;
import org.quartz.Scheduler;
import org.quartz.SchedulerFactory;
import org.quartz.Trigger;
import org.quartz.impl.StdSchedulerFactory;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import java.util.Date;

/**
 * This Example will demonstrate how to start and shutdown the Quartz scheduler and how to schedule
 * a job to run in Quartz.
 * 
 * @author Bill Kratzer
 */
public class SimpleCronExample {

    public void run() throws Exception {
	Logger log = LoggerFactory.getLogger(SimpleCronExample.class);

	log.info("------- Initializing ----------------------");

	// 定义调度器
	SchedulerFactory sf = new StdSchedulerFactory();
	Scheduler sched = sf.getScheduler();

	log.info("------- Initialization Complete -----------");

	// 获取当前时间的下一分钟
	Date runTime = evenMinuteDate(new Date());

	log.info("------- Scheduling Job  -------------------");

	// 定义job
	JobDetail job = newJob(HelloJob.class).withIdentity("job1", "group1").build();

	// 定义触发器，每2秒执行一次
	Trigger trigger = newTrigger().withIdentity("trigger1", "group1").withSchedule(cronSchedule("0 0/1 * * * ?")).build();

	// 将job注册到调度器
	sched.scheduleJob(job, trigger);
	log.info(job.getKey() + " will run at: " + runTime);

	// 启动调度器
	sched.start();

	log.info("------- Started Scheduler -----------------");

	// 等待1分钟
	log.info("------- Waiting 60 seconds... -------------");
	try {
	    Thread.sleep(60L * 1000L);
	} catch (Exception e) {
	    //
	}

	// 关闭调度器
	log.info("------- Shutting Down ---------------------");
	sched.shutdown(true);
	log.info("------- Shutdown Complete -----------------");
    }

    public static void main(String[] args) throws Exception {

	SimpleCronExample example = new SimpleCronExample();
	example.run();

    }

}
```
### 3 spring整合quartz
#### 3.1 导入依赖
```
<dependency>
	<groupId>org.quartz-scheduler</groupId>
	<artifactId>quartz</artifactId>
	<version>2.2.1</version>
</dependency>
<!-- 支持spring整合第三方框架支持包 -->
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context-support</artifactId>
	<version>4.0.6.RELEASE</version>
</dependency>
```
#### 3.2 Job类继承抽象类QuartzJobBean实现其中的executeInternal方法
```java
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;
import org.springframework.context.ApplicationContext;
import org.springframework.scheduling.quartz.QuartzJobBean;

public class MyJob extends QuartzJobBean {

    @Override
    protected void executeInternal(JobExecutionContext context) throws JobExecutionException {
	System.out.println("myJob 执行了............." + context.getTrigger().getKey().getName());
	ApplicationContext applicationContext = (ApplicationContext) context.getJobDetail().getJobDataMap().get("applicationContext"); // 配置中设置applicationContext
	System.out.println("获取到的Spring容器是： " + applicationContext);

    }
}
```
#### <span id="jump">3.3 加载scheduler配置文件(启动调度器)</span>
```java
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {

    public static void main(String[] args) {
	new ClassPathXmlApplicationContext("classpath:applicationContext-scheduler.xml");
    }

}
```
##### 3.3.1 applicationContext-scheduler.xml配置文件
>* 1.定义任务job类
>* 2.定义表达式触发器
>* 3.定义调度器并注册触发
>* [4.启动spring容器(启动调度器)](#jump)

```
<!-- 定义任务bean -->
	<bean name="myJobDetail" class="org.springframework.scheduling.quartz.JobDetailFactoryBean">
		<!-- 指定具体的job类 -->
		<property name="jobClass" value="cn.test.quartz.MyJob" />
		<!-- 指定job的名称 -->
		<property name="name" value="myJob" />
		<!-- 指定job的分组 -->
		<property name="group" value="jobs" />
		<!-- 必须设置为true，如果为false，当没有活动的触发器与之关联时会在调度器中删除该任务 -->
		<property name="durability" value="true" />
		<!-- 指定spring容器的key，如果不设定在job中的jobmap中是获取不到spring容器的 -->
		<property name="applicationContextJobDataKey" value="applicationContext" />
	</bean>

	<!-- 定义触发器 -->
	<bean id="cronTrigger" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
		<!-- 一个触发器只能有一个job，因为CronTriggerFactoryBean中没有setJobDetails -->
		<property name="jobDetail" ref="myJobDetail" />
		<!-- 每一分钟执行一次 -->
		<property name="cronExpression" value="0/5 * * * * ?" />
	</bean>

	<!-- 定义触发器 -->
	<!-- 演示：一个job可以有多个trigger； -->
	<!-- <bean id="cronTrigger2" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean"> <property name="jobDetail" ref="myJobDetail" /> 每一分钟执行一次 <property name="cronExpression" value="0 */1 
		* * * ?" /> </bean> -->
	<!-- 定义调度器 -->
	<bean class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
		<property name="triggers">
			<list>
				<ref bean="cronTrigger" />
				<ref bean="cronTrigger2" />
			</list>
		</property>
	</bean>
```
### 4 使用org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor多线程执行任务
ThreadPoolTaskExecutor线程池，用来并行执行每个对应的job，提高效率，这也是上面提到不推荐使用jdk自身timer的一个很重要的原因
#### 4.1 job类
```java
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;

public class MyJobThread {
    public void service() {
	ThreadPoolTaskExecutor jobPropertyConfigurer = (ThreadPoolTaskExecutor) ContextUtil.getBean("executor");
	jobPropertyConfigurer.execute(new TestThread()); // 单个线程加入到线程池中执行，可加入多个线程到线程池
    }
}
```
#### 4.2 需要执行的线程类
```java
/**
 * @ClassName TestThread
 * @Author volc
 * @Description 需要执行的线程类
 * @Date 2017年4月10日 上午12:55:36
 */
public class TestThread implements Runnable {
    public void run() {
	System.out.println("执行了线程");
    }
}
```
#### 4.3 启动调度器
```java
import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * @ClassName ContextUtil
 * @Author volc
 * @Description 获取ApplicationContext对象工具类，用于方取出注入的bean
 * @Date 2017年4月10日 上午12:08:58
 */
public class ContextUtil {

    private static ApplicationContext context;

    public static ApplicationContext getContext() {
	return context;
    }

    public static Object getBean(String beanName) {
	return context.getBean(beanName);
    }

    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
	context = applicationContext;
    }

    // 启动调度器
    public static void main(String[] args) {
	context = new ClassPathXmlApplicationContext(new String[] { "applicationContext-schedulerThread.xml" });
    }

}
```
#### 4.4 scheduler配置文件applicationContext-schedulerThread.xml
```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p" xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-3.0.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd">

	<!-- 线程执行器配置，用于任务注册 -->
	<bean id="executor" class="org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor">
		<property name="corePoolSize" value="10" />
		<property name="maxPoolSize" value="100" />
		<property name="queueCapacity" value="500" />
	</bean>

	<bean id="testJobTask" class="cn.test.quartz.MyJobThread" />

	<!-- 定义任务bean -->
	<bean name="myJobDetail" class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
		<property name="targetObject" ref="testJobTask" />
		<property name="targetMethod" value="service" />
	</bean>

	<!-- 定义触发器 -->
	<bean id="cronTrigger" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
		<property name="jobDetail" ref="myJobDetail" />
		<!-- 每一分钟执行一次 -->
		<property name="cronExpression" value="0/5 * * * * ?" />
	</bean>

	<!-- 定义触发器 -->
	<!-- 演示：一个job可以有多个trigger； -->
	<!-- <bean id="cronTrigger2" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean"> <property name="jobDetail" ref="myJobDetail" /> 每一分钟执行一次 <property name="cronExpression" value="0 */1 
		* * * ?" /> </bean> -->
	<!-- 定义调度器 -->
	<bean class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
		<property name="triggers">
			<list>
				<ref bean="cronTrigger" />
				<!-- <ref bean="cronTrigger2" /> -->
			</list>
		</property>
		<!-- ThreadPoolTaskExecutor，线程池，用来并行执行每个对应的job，提高效率，这也是上面提到不推荐使用jdk自身timer的一个很重要的原因 -->
		<property name="taskExecutor" ref="executor" />
	</bean>

</beans>

```
```
执行了线程
2017-04-10 00:58:30,016 [org.springframework.scheduling.quartz.SchedulerFactoryBean#0_QuartzSchedulerThread] [org.quartz.core.QuartzSchedulerThread]-[DEBUG] batch acquisition of 1 triggers
2017-04-10 00:58:35,004 [executor-4] [org.quartz.core.JobRunShell]-[DEBUG] Calling execute on job DEFAULT.myJobDetail
2017-04-10 00:58:35,004 [org.springframework.scheduling.quartz.SchedulerFactoryBean#0_QuartzSchedulerThread] [org.quartz.core.QuartzSchedulerThread]-[DEBUG] batch acquisition of 1 triggers
2017-04-10 00:58:35,004 [executor-4] [org.springframework.beans.factory.support.DefaultListableBeanFactory]-[DEBUG] Returning cached instance of singleton bean 'executor'
执行了线程
2017-04-10 00:58:40,011 [executor-5] [org.quartz.core.JobRunShell]-[DEBUG] Calling execute on job DEFAULT.myJobDetail
2017-04-10 00:58:40,011 [executor-5] [org.springframework.beans.factory.support.DefaultListableBeanFactory]-[DEBUG] Returning cached instance of singleton bean 'executor'
执行了线程
2017-04-10 00:58:40,012 [org.springframework.scheduling.quartz.SchedulerFactoryBean#0_QuartzSchedulerThread] [org.quartz.core.QuartzSchedulerThread]-[DEBUG] batch acquisition of 1 triggers
2017-04-10 00:58:45,001 [executor-7] [org.quartz.core.JobRunShell]-[DEBUG] Calling execute on job DEFAULT.myJobDetail
2017-04-10 00:58:45,001 [org.springframework.scheduling.quartz.SchedulerFactoryBean#0_QuartzSchedulerThread] [org.quartz.core.QuartzSchedulerThread]-[DEBUG] batch acquisition of 1 triggers
2017-04-10 00:58:45,001 [executor-7] [org.springframework.beans.factory.support.DefaultListableBeanFactory]-[DEBUG] Returning cached instance of singleton bean 'executor'
执行了线程
```
***
### 5 quratz cron表达式
```
1.	CronTrigger
CronTriggers往往比SimpleTrigger更有用，如果您需要基于日历的概念，而非SimpleTrigger完全指定的时间间隔，复发的发射工作的时间表。
CronTrigger，你可以指定触发的时间表如“每星期五中午”，或“每个工作日9:30时”，甚至“每5分钟一班9:00和10:00逢星期一上午，星期三星期五“。
即便如此，SimpleTrigger一样，CronTrigger拥有的startTime指定的时间表时生效，指定的时间表时，应停止（可选）结束时间。
2.	Cron Expressions
cron的表达式被用来配置CronTrigger实例。 cron的表达式是字符串，实际上是由七子表达式，描述个别细节的时间表。这些子表达式是分开的空白，代表：
1.	1.        Seconds
2.	2.        Minutes
3.	3.        Hours
4.	4.        Day-of-Month
5.	5.        Month
6.	6.        Day-of-Week
7.	7.        Year (可选字段)
例  "0 0 12 ? * WED" 在每星期三下午12:00 执行,
个别子表达式可以包含范围, 例如，在前面的例子里("WED")可以替换成 "MON-FRI", "MON, WED, FRI"甚至"MON-WED,SAT".
“*” 代表整个时间段.
每一个字段都有一套可以指定有效值，如
Seconds (秒)         ：可以用数字0－59 表示，
Minutes(分)          ：可以用数字0－59 表示，
Hours(时)             ：可以用数字0-23表示,
Day-of-Month(天) ：可以用数字1-31 中的任一一个值，但要注意一些特别的月份
Month(月)            ：可以用0-11 或用字符串  “JAN, FEB, MAR, APR, MAY, JUN, JUL, AUG, SEP, OCT, NOV and DEC” 表示
Day-of-Week(每周)：可以用数字1-7表示（1 ＝ 星期日）或用字符口串“SUN, MON, TUE, WED, THU, FRI and SAT”表示
“/”：为特别单位，表示为“每”如“0/15”表示每隔15分钟执行一次,“0”表示为从“0”分开始, “3/20”表示表示每隔20分钟执行一次，“3”表示从第3分钟开始执行
“?”：表示每月的某一天，或第周的某一天
“L”：用于每月，或每周，表示为每月的最后一天，或每个月的最后星期几如“6L”表示“每月的最后一个星期五”
“W”：表示为最近工作日，如“15W”放在每月（day-of-month）字段上表示为“到本月15日最近的工作日”
““#”：是用来指定“的”每月第n个工作日,例 在每周（day-of-week）这个字段中内容为"6#3" or "FRI#3" 则表示“每月第三个星期五”
 
1）Cron表达式的格式：秒 分 时 日 月 周 年(可选)。
               字段名                 允许的值                        允许的特殊字符  
               秒                         0-59                               , - * /  
               分                         0-59                               , - * /  
               小时                     0-23                               , - * /  
               日                         1-31                               , - * ? / L W C  
               月                         1-12 or JAN-DEC         , - * /  
               周几                     1-7 or SUN-SAT           , - * ? / L C #  
               年 (可选字段)     empty, 1970-2099      , - * /
 
               “?”字符：表示不确定的值
               “,”字符：指定数个值
               “-”字符：指定一个值的范围
               “/”字符：指定一个值的增加幅度。n/m表示从n开始，每次增加m
               “L”字符：用在日表示一个月中的最后一天，用在周表示该月最后一个星期X
               “W”字符：指定离给定日期最近的工作日(周一到周五)
               “#”字符：表示该月第几个周X。6#3表示该月第3个周五
 
 
         2）Cron表达式范例：
                 每隔5秒执行一次：*/5 * * * * ?
                 每隔1分钟执行一次：0 */1 * * * ?
                 每天23点执行一次：0 0 23 * * ?
                 每天凌晨1点执行一次：0 0 1 * * ?
                 每月1号凌晨1点执行一次：0 0 1 1 * ?
                 每月最后一天23点执行一次：0 0 23 L * ?
                 每周星期天凌晨1点实行一次：0 0 1 ? * L
                 在26分、29分、33分执行一次：0 26,29,33 * * * ?
                 每天的0点、13点、18点、21点都执行一次：0 0 0,13,18,21 * * ?
```
生成cron表达式工具:
<a href="http://pan.baidu.com/s/1pKZKQWb">CronExpBuilder-1.0(触发器表达式生成器).jar</a>

### 6 使用springmvc中的定时任务
参考：https://www.cnblogs.com/liaojie970/p/5913272.html

```java
import com.alibaba.druid.support.logging.Log;
import com.alibaba.druid.support.logging.LogFactory;
import com.baomidou.springwind.common.ResponseInfo;
import com.baomidou.springwind.service.service.WeatherService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;
import java.io.IOException;

/**
 * @Author: volc
 * @Description: 定时获取天气并保存
 * @Date: 11:47 2018/1/12
 */
@Component
public class WeatherTask {
    @Autowired
    private WeatherService weatherService;
    private static Log logger = LogFactory.getLog(WeatherTask.class);

    // 0 0/60 * * * ? 每个一小时执行
    @Scheduled(cron = "0/10 * * * * ?") // 间隔5秒执行
    public void getWeather() throws IOException {
        ResponseInfo responseInfo = weatherService.getWeather();
        if (responseInfo.getSuccess()) { // 获取天气并保存成功
            logger.debug("获取天气并保存成功");
        }
    }
}
```

配置
servlet入口文件
<!-- 配置任务扫描 -->
<task:annotation-driven />
```
<!-- 扫描任务 -->
<context:component-scan base-package="com.baomidou.springwind.task" />
```

配置文件
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd
       http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.2.xsd http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task.xsd">

    <!-- 扫描 controller -->
    <context:component-scan base-package="com.baomidou.springwind.controller"/>

    <!-- 扫描任务 -->
    <context:component-scan base-package="com.baomidou.springwind.task" />

    <!-- 定义视图解析器 -->
    <bean id="velocityConfig"
          class="org.springframework.web.servlet.view.velocity.VelocityConfigurer">
        <property name="resourceLoaderPath" value="/WEB-INF/views/"/>
        <property name="configLocation" value="classpath:properties/velocity.properties"/>
    </bean>
    <bean id="viewResolver"
          class="org.springframework.web.servlet.view.velocity.VelocityLayoutViewResolver">
        <property name="suffix" value=".html"/>
        <property name="cache" value="false"/>
        <property name="contentType" value="text/html;charset=utf-8"/>
        <property name="dateToolAttribute" value="date"/><!--日期函数名称-->
        <property name="numberToolAttribute" value="number"/><!--数字函数名称-->
        <property name="layoutUrl" value="layout/default.vm"/>
        <property name="toolboxConfigLocation" value="/WEB-INF/toolbox.xml"/>
    </bean>

    <!-- 重定向视图控制器标签 -->
    <mvc:redirect-view-controller path="/redirect"
                                  redirect-url="/status" context-relative="true" status-code="301"
                                  keep-query-params="true"/>

    <mvc:default-servlet-handler/>

    <!-- 状态控制器标签 -->
    <mvc:status-controller path="/status" status-code="200"/>

    <!-- 带状态的视图控制器标签 -->
    <mvc:view-controller path="/error/**" status-code="200"/>

    <!-- 配置静态资源，直接映射到对应的文件夹，DispatcherServlet 不处理 -->
    <mvc:resources mapping="/static/**" location="/WEB-INF/static/"/>

    <!-- 网站图标，注意 ！favicon.ico 放在 webapp 目录 -->
    <mvc:resources mapping="/favicon.ico" location="/favicon.ico"/>

    <!-- 获取ApplicationContext上下文 -->
    <bean id="springContextHolder" class="com.baomidou.framework.spring.SpringContextHolder"/>

    <!-- KISSO 初始化 -->
    <bean id="kissoInit" class="com.baomidou.kisso.web.WebKissoConfigurer" init-method="initKisso">
        <property name="ssoPropPath" value="properties/sso.properties"/>
    </bean>

    <!--<mvc:interceptors>-->
    <!--&lt;!&ndash; SSO 拦截器 &ndash;&gt;-->
    <!--&lt;!&ndash; path 对所有的请求拦截使用/**，对某个模块下的请求拦截使用：/myPath/* &ndash;&gt;-->
    <!--<mvc:interceptor>-->
    <!--<mvc:mapping path="/**" />-->
    <!--<bean class="com.baomidou.kisso.web.interceptor.SSOSpringInterceptor" />-->
    <!--</mvc:interceptor>-->
    <!--&lt;!&ndash; 权限拦截器 &ndash;&gt;-->
    <!--<mvc:interceptor>-->
    <!--<mvc:mapping path="/**" />-->
    <!--<bean class="com.baomidou.kisso.web.interceptor.SSOPermissionInterceptor">-->
    <!--<property name="authorization">-->
    <!--<bean class="com.baomidou.springwind.service.impl.PermissionServiceImpl"/>-->
    <!--</property>-->
    <!--<property name="illegalUrl" value="/error/illegalAccess.html"/>-->
    <!--</bean>-->
    <!--</mvc:interceptor>-->
    <!--<bean class="com.baomidou.framework.spring.DataLoadingInterceptor" />-->
    <!--</mvc:interceptors>-->

    <mvc:annotation-driven>
        <!-- 编码转换 -->
        <mvc:message-converters>
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <property name="supportedMediaTypes">
                    <list>
                        <value>text/plain;charset=UTF-8</value>
                        <value>text/html;charset=UTF-8</value>
                        <value>application/json;charset=UTF-8</value>
                    </list>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
</beans>
```

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	   xmlns:context="http://www.springframework.org/schema/context"
	   xmlns:tx="http://www.springframework.org/schema/tx"
	   xmlns:aop="http://www.springframework.org/schema/aop" xmlns:task="http://www.springframework.org/schema/task"
	   xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
	   http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd
       http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.2.xsd http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task.xsd">

	<!-- 使用annotation注解方式配置事务 -->
	<tx:annotation-driven transaction-manager="transactionManager" />
	<!-- 配置任务扫描 -->
	<task:annotation-driven />

	<!-- 使用JDBC事物 -->
	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource" />
	</bean>
	
	<!-- AOP配置事物 -->
	<tx:advice id="transactionAdvice" transaction-manager="transactionManager">
		<tx:attributes>
			<tx:method name="select*" read-only="true" propagation="REQUIRED" />
			<tx:method name="delete*"  propagation="REQUIRED" rollback-for="Exception" />
			<tx:method name="update*" propagation="REQUIRED" rollback-for="Exception" />
			<tx:method name="insert*" propagation="REQUIRED" rollback-for="Exception" />
			<tx:method name="*" propagation="REQUIRED" />
		</tx:attributes>
	</tx:advice>
	
	<!-- 配置AOP切面 -->
	<aop:config>
        <aop:pointcut id="transactionPointcut" expression="execution(* com.baomidou.springwind.service.impl..*.*(..))"/>
        <aop:advisor pointcut-ref="transactionPointcut" advice-ref="transactionAdvice"/>
    </aop:config>

</beans>
```
