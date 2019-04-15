---
title: jmeter
date: 2018-04-19 22:28:02
update: 2018-04-19 22:28:02
categories: JMETER
tags: [jmeter]
---

### Jmeter压力测试

<!-- more -->

#### 创建线程组
![a](https://volc1612.gitee.io/blog/images/jmeter/a.png)

#### 参数说明
![a1](https://volc1612.gitee.io/blog/images/jmeter/a1.png)

#### http请求
##### 创建请求头
![c](https://volc1612.gitee.io/blog/images/jmeter/c.png)

##### 设置参数
###### 普通请求
post请求方式，数据格式为json字符串

Content-Type : application/json

![c1](https://volc1612.gitee.io/blog/images/jmeter/c1.png)
###### 提交文件

MIME Type：application/octet-stream

![c2](https://volc1612.gitee.io/blog/images/jmeter/c2.png)

#### 添加监听器查看结果
![d](https://volc1612.gitee.io/blog/images/jmeter/d.png)
![d1](https://volc1612.gitee.io/blog/images/jmeter/d1.png)
![d2](https://volc1612.gitee.io/blog/images/jmeter/d2.png)
![d3](https://volc1612.gitee.io/blog/images/jmeter/d3.png)

保存文件
##### 运行
点击运行按钮，可在监听器中查看结果
![e](https://volc1612.gitee.io/blog/images/jmeter/e.png)
