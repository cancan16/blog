---
title: Elasticsearch相关学习
date: 2020-04-20 20:45:30
update: 2020-04-20 20:45:30
categories: Elasticsearch
tags: [Elasticsearch]
---

<div style="text-align: center"><iframe height="60" width="260" src="https://www.ximalaya.com/thirdparty/player/sound/player.html?id=285041831&type=red" frameborder=0 allowfullscreen></iframe></div>

<img style="width: 80%;height:80%" src="https://volc1612.gitee.io/blog/images/elasticsearch/布达拉宫.jpg"/>

<!-- more -->

### 纲要思维导图

<details>
<summary>点击展开思维导图</summary>
<iframe id="embed_dom" name="embed_dom" frameborder="0" style="display:block;width:100%; height:400px;" src="https://www.processon.com/embed/5e9da6c5f346fb177b86bd70"></iframe>
</details>

### 为什么不能用`MySQL`作为搜索引擎？

* 数据类型

    全文索引搜索很好支持非结构化数据的搜索，可以更好快速搜索大量存在任何单词非结构化的文本。例如google，百度，根据网页中的关键字生成索引，我们在搜索的时候输入关键字，它们会将匹配到该关键字即匹配到索引的所有网页返回，还有常见中使用日志的搜索。对于这种非结构化的文本数据，关系型数据库搜索不能够很好的支持。

* 搜索性能

    对于`MySQL`当达到千万级别数据量时，使用`like`关键字效率明显变慢。

* `ES`提供灵活搜索能力    

    * 提供展示类似关键

        ![es灵活索引](https://volc1612.gitee.io/blog/images/elasticsearch/es灵活索引-a.png)
    * 输入`总冠军`，用户也行展示带有`冠军`关键字的文章
        ![es灵活索引](https://volc1612.gitee.io/blog/images/elasticsearch/es灵活索引-b.png)

* 索引的维护

    一般关系型数据库全文索引都很鸡肋，因为一般情况下也没有人用数据库存长文本字段，因为进行全文索引的时候需要扫描整个表，如果数据量大，即使优化SQL效果也是不明显。及时建立索引，但是维护起来也很麻烦，对于`insert`和`update`操作都会重新构建索引。

### 全文搜索引擎的使用场景

* 数据是大量非结构化的文本。
* 文本数据达到数十万或百万级别，甚至更多。
* 基于大量交互式文本查询
* 需求非常灵活的索引查询，比如推荐一些关键字
* 对安全事务，非文本数据操作较少的情况(读多写少的情况)

### 常见的搜索引擎

#### Lucene

* Lucene是⼀个Java全⽂搜索引擎，完全⽤Java编写。Lucene不是⼀个完整的应⽤程序，⽽是
⼀个代码库和API，可以很容易地⽤于向应⽤程序添加搜索功能。

* 通过简单的API提供强⼤的功能
    * 可扩展的⾼性能索引
    * 强⼤，准确，⾼效的搜索算法
    * 跨平台解决⽅案
* Apache软件基⾦会
    * 在Apache软件基⾦会提供的开源软件项⽬的Apache社区的⽀持。
    * 但是Lucene只是⼀个框架，要充分利⽤它的功能，需要使⽤java，并且在程序中集成
Lucene。需要很多的学习了解，才能明⽩它是如何运⾏的，熟练运⽤Lucene确实⾮常
复杂。

#### Solr

* Solr是⼀个基于Lucene的Java库构建的开源搜索平台。它以⽤户友好的⽅式提供Apache
Lucene的搜索功能。它是⼀个成熟的产品，拥有强⼤⽽⼴泛的⽤户社区。它能提供分布式索
引，复制，负载均衡查询以及⾃动故障转移和恢复。如果它被正确部署然后管理得好，它就
能够成为⼀个⾼度可靠，可扩展且容错的搜索引擎。很多互联⽹巨头，如Netflix，eBay，
Instagram和亚⻢逊都使⽤Solr，因为它能够索引和搜索多个站点。
* 强⼤的功能
    * 全⽂搜索
    * 突出
    * 分⾯搜索
    * 实时索引
    * 动态群集
    * 数据库集成
    * NoSQL功能和丰富的⽂档处理

#### Elasticsearch

* Elasticsearch是⼀个开源，是⼀个基于Apache Lucene库构建的Restful搜索引擎.
* Elasticsearch是在Solr之后⼏年推出的。它提供了⼀个分布式，多租户能⼒的全⽂搜索引
擎，具有HTTP Web界⾯（REST）和⽆架构JSON⽂档。Elasticsearch的官⽅客户端库提供
Java，Groovy，PHP，Ruby，Perl，Python，.NET和Javascript。
* 主要功能
    * 分布式搜索
    * 数据分析
    * 分组和聚合
* 应⽤场景
    * 维基百科
    * Stack Overflow
    * GitHub
    * 电商⽹站
    * ⽇志数据分析
    * 商品价格监控⽹站
    * BI系统
    * 站内搜索
    * 篮球论坛