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
    * 输入`总冠军`，用户也希望展示带有`冠军`关键字的文章
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

### ES的安装

<a href="https://www.elastic.co/downloads/past-releases#elasticsearch">下载历史版本</a>

```sh
# 解压
[root@localhost es]# tar -xzf elasticsearch-7.7.0-linux-x86_64.tar.gz
# 启动
[root@localhost elasticsearch-7.7.0]# ./bin/elasticsearch
# 报错
ERROR: [3] bootstrap checks failed
[1]: max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]
[2]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
[3]: the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured
ERROR: Elasticsearch did not exit normally - check the logs at /usr/local/src/es/elasticsearch-7.7.0/logs/elasticsearch.log
[2020-05-17T16:59:26,942][INFO ][o.e.n.Node               ] [localhost.localdomain] stopping ...
[2020-05-17T16:59:27,057][INFO ][o.e.n.Node               ] [localhost.localdomain] stopped
[2020-05-17T16:59:27,058][INFO ][o.e.n.Node               ] [localhost.localdomain] closing ...
[2020-05-17T16:59:27,089][INFO ][o.e.n.Node               ] [localhost.localdomain] closed
[2020-05-17T16:59:27,090][INFO ][o.e.x.m.p.NativeController] [localhost.localdomain] Native controller process has stopped - no new native processes can be started
```

#### 常见错误

##### 解决不能使用root账号启动的错误

* 添加用户组和用户并设置密码，设置es安装目录的权限归属

    ```sh
    [root@localhost src]# groupadd es
    [root@localhost src]# useradd es -g es
    [root@localhost src]# passwd es
    Changing password for user es.
    New password: 
    BAD PASSWORD: The password is shorter than 8 characters
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    [root@localhost src]# cd es
    [root@localhost es]# pwd
    /usr/local/src/es
    # 将/usr/local/src/es目录权限归属于es组中的es用户
    [root@localhost es]# chown -R es:es /usr/local/src/es
    ```
* 使用es用户启动es

    ```sh
    [es@localhost bin]$ ./elasticsearch
    [2020-05-17T16:41:32,617][INFO ][o.e.e.NodeEnvironment    ] [localhost.localdomain] using [1] data paths, mounts [[/ (rootfs)]], net usable_space [2.8gb], net total_space [15.4gb], types [rootfs]
    [2020-05-17T16:41:32,619][INFO ][o.e.e.NodeEnvironment    ] [localhost.localdomain] heap size [1gb], compressed ordinary object pointers [true]
    [2020-05-17T16:41:32,719][INFO ][o.e.n.Node               ] [localhost.localdomain] node name [localhost.localdomain], node ID [vS1Lv6gTR66LatxWLN9Vfg], cluster name [elasticsearch]
    [2020-05-17T16:41:32,724][INFO ][o.e.n.Node               ] [localhost.localdomain] version[7.7.0], pid[12328], build[default/tar/81a1e9eda8e6183f5237786246f6dced26a10eaf/2020-05-12T02:01:37.602180Z], OS[Linux/3.10.0-957.el7.x86_64/amd64], JVM[AdoptOpenJDK/OpenJDK 64-Bit Server VM/14/14+36]
    [2020-05-17T16:41:32,724][INFO ][o.e.n.Node               ] [localhost.localdomain] JVM home [/usr/local/src/es/elasticsearch-7.7.0/jdk]
    [2020-05-17T16:41:32,724][INFO ][o.e.n.Node               ] [localhost.localdomain] JVM arguments [-Xshare:auto, -Des.networkaddress.cache.ttl=60, -Des.networkaddress.cache.negative.ttl=10, -XX:+AlwaysPreTouch, -Xss1m, -Djava.awt.headless=true, -Dfile.encoding=UTF-8, -Djna.nosys=true, -XX:-OmitStackTraceInFastThrow, -XX:+ShowCodeDetailsInExceptionMessages, -Dio.netty.noUnsafe=true, -Dio.netty.noKeySetOptimization=true, -Dio.netty.recycler.maxCapacityPerThread=0, -Dio.netty.allocator.numDirectArenas=0, -Dlog4j.shutdownHookEnabled=false, -Dlog4j2.disable.jmx=true, -Djava.locale.providers=SPI,COMPAT, -Xms1g, -Xmx1g, -XX:+UseG1GC, -XX:G1ReservePercent=25, -XX:InitiatingHeapOccupancyPercent=30, -Djava.io.tmpdir=/tmp/elasticsearch-2906376876676420818, -XX:+HeapDumpOnOutOfMemoryError, -XX:HeapDumpPath=data, -XX:ErrorFile=logs/hs_err_pid%p.log, -Xlog:gc*,gc+age=trace,safepoint:file=logs/gc.log:utctime,pid,tags:filecount=32,filesize=64m, -XX:MaxDirectMemorySize=536870912, -Des.path.home=/usr/local/src/es/elasticsearch-7.7.0, -Des.path.conf=/usr/local/src/es/elasticsearch-7.7.0/config, -Des.distribution.flavor=default, -Des.distribution.type=tar, -Des.bundled_jdk=true]
    [2020-05-17T16:41:37,436][INFO ][o.e.p.PluginsService     ] [localhost.localdomain] loaded module [aggs-matrix-stats]
    ```

##### 外网无法访问es问题


默认情况下，`Elastic`只允许本机访问，如果需要远程访问，可以修改`Elastic`安装目录的`config/elasticsearch.yml`文件，去掉`network.host`的注释，将它的值改成`0.0.0.0`，然后重新启动 `Elastic`

##### 启动错误

```sh
ERROR: [3] bootstrap checks failed
[1]: max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]
[2]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
[3]: the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured
ERROR: Elasticsearch did not exit normally - check the logs at /usr/local/src/es/elasticsearch-7.7.0/logs/elasticsearch.log
[2020-05-17T16:59:26,942][INFO ][o.e.n.Node               ] [localhost.localdomain] stopping ...
[2020-05-17T16:59:27,057][INFO ][o.e.n.Node               ] [localhost.localdomain] stopped
[2020-05-17T16:59:27,058][INFO ][o.e.n.Node               ] [localhost.localdomain] closing ...
[2020-05-17T16:59:27,089][INFO ][o.e.n.Node               ] [localhost.localdomain] closed
[2020-05-17T16:59:27,090][INFO ][o.e.x.m.p.NativeController] [localhost.localdomain] Native controller process has stopped - no new native processes can be started
```

* 错误一：问题翻译过来就是：`elasticsearch`用户拥有的可创建文件描述的权限太低，至少需要`65536`；

    解决方法：
    切换到root用户修改`vim /etc/security/limits.conf`，在最后面追加下面内容

    ```
    * soft nofile 65536
    * hard nofile 131072
    * soft nproc 2048
    * hard nproc 4096
    ```
* 错误二：分配给es服务内存太小   

    解决方法：
    切换到root账号
    ```sh
    [root@localhost bin]# sudo sysctl -w vm.max_map_count=262144
    vm.max_map_count = 262144
    ```
* 错误三：es推荐使用集群，需要指定主节点配置

    ```yml
    cluster.name: "docker-cluster"
    # custom config
    node.name: "node-1"
    discovery.seed_hosts: ["127.0.0.1", "[::1]"]
    cluster.initial_master_nodes: ["node-1"]
    # 开启跨域访问支持，默认为false
    http.cors.enabled: true
    # 跨域访问允许的域名地址，(允许所有域名)以上使用正则
    http.cors.allow-origin: /.*/ 
    ```

* 错误四（没有遇到）：
    报错原因：这是在因为`Centos7`不支持`SecComp`，而`ES5.2.0`以上默认`bootstrap.system_call_filter`为`true`进行检测，所以导致检测失败，失败后直接导致`ES`不能启动。
    解决办法：在`elasticsearch.yml`中配置`bootstrap.system_call_filter`为`false`，注意要在`Memory`下面

    ```yml
    bootstrap.memory_lock: false
    bootstrap.system_call_filter: false
    ```

    
#### 启动

```sh
[es@localhost bin]$ ./elasticsearch
....
[2020-05-17T17:39:06,658][INFO ][o.e.n.Node               ] [node-1] started
[2020-05-17T17:39:07,006][INFO ][o.e.l.LicenseService     ] [node-1] license [4f02f9f0-bf32-430b-bf23-e5b0a8115346] mode [basic] - valid
[2020-05-17T17:39:07,006][INFO ][o.e.x.s.s.SecurityStatusChangeListener] [node-1] Active license is now [BASIC]; Security is disabled
[2020-05-17T17:39:07,036][INFO ][o.e.g.GatewayService     ] [node-1] recovered [0] indices into cluster_state
```

#### 访问es

外网访问：`http://192.168.25.11:9200`

```json
{
"name" : "node-1",
"cluster_name" : "my-application",
"cluster_uuid" : "Ha-D_Ls0RMmcu89lbnTcyw",
"version" : {
    "number" : "7.7.0",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "81a1e9eda8e6183f5237786246f6dced26a10eaf",
    "build_date" : "2020-05-12T02:01:37.602180Z",
    "build_snapshot" : false,
    "lucene_version" : "8.5.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
},
"tagline" : "You Know, for Search"
}
```

### es目录结构


|类型 |描述| 默认位置| 设置|
|---|---|---|---|
|bin |⼆进制脚本包含启动节点的elasticsearch |{path.home}/bin||
|conf |配置⽂件包含elasticsearch.yml |{path.home}/config| path.conf|
|data| 在节点上申请的每个index/shard的数据⽂件的位置。可容纳多个位置 |{path.home}/data| path.data|
|logs| ⽇志⽂件位置 |{path.home}/logs |path.logs|
|plugins |插件⽂件位置。每个插件将包含在⼀个⼦⽬录中。| {path.home}/plugins |path.plugins|

### ES相关概念

* 索引(index)

    ⼀个索引可以理解成⼀个关系型数据库。
* 类型(type)

    ⼀种type就像⼀类表，⽐如user表，order表。
    注意：
    ES 5.x中⼀个index可以有多种type。
    ES 6.x中⼀个index只能有⼀种type。
    ES 7.x以后已经移除type这个概念。
* 映射(mapping)

    mapping定义了每个字段的类型等信息。相当于关系型数据库中的表结构。
* ⽂档(document)

    ⼀个document相当于关系型数据库中的⼀⾏记录。
* 字段(field)

    相当于关系型数据库表的字段
* 集群(cluster)

    集群由⼀个或多个节点组成，⼀个集群有⼀个默认名称"elasticsearch"。
* 节点(node)

    集群的节点，⼀台机器或者⼀个进程
* 分⽚和副本(shard)

    副本是分⽚的副本。分⽚有主分⽚(primary Shard)和副本分⽚(replica Shard)之分，当主分片挂机，福分片可代替主分片。
    ⼀个Index数据在物理上被分布在多个主分⽚中，每个主分⽚只存放部分数据。
    每个主分⽚可以有多个副本，叫副本分⽚，是主分⽚的复制。

### ES基于RESTful风格的api来设计的

* 方法

|action| 描述|
|---|---|
|HEAD |只获取某个资源的头部信息|
|GET |获取资源|
|POST |创建或更新资源|
|PUT |创建或更新资源|
|DELETE |删除资源|

* GET /user:列出所有的⽤户
* POST /user:新建⼀个⽤户
* PUT /user:更新某个指定⽤户的信息
* DELETE /user/ID:删除指定⽤户

### 索引的介绍和使用

* 新增

    ```
    curl -X PUT "localhost:9200/nba"
    ```

    ```json
    {
        "acknowledged": true,
        "shards_acknowledged": true,
        "index": "nba"
    }
    ```

* 获取

    ```
    curl -X GET "localhost:9200/nba"
    ```

    ```json
    {
        "nba": {
            "aliases": {},
            "mappings": {},
            "settings": {
                "index": {
                    "creation_date": "1589720592678",
                    "number_of_shards": "1",
                    "number_of_replicas": "1",
                    "uuid": "8rfw1l5QQVGsN3psbb5UUA",
                    "version": {
                        "created": "7070099"
                    },
                    "provided_name": "nba"
                }
            }
        }
    }
    ```

* 删除

    ```
    curl -X DELETE "localhost:9200/nba"
    ```

    ```json
    {
        "acknowledged": true
    }
    ```

* 根据多个索引名称批量获取

    ```
    curl -x GET "localhost:9200/nba,cba"
    ```

    ```json
    {
        "cba": {
            "aliases": {},
            "mappings": {},
            "settings": {
                "index": {
                    "creation_date": "1589720765271",
                    "number_of_shards": "1",
                    "number_of_replicas": "1",
                    "uuid": "fY4nCD0qTB-nxcAzkrvQtg",
                    "version": {
                        "created": "7070099"
                    },
                    "provided_name": "cba"
                }
            }
        },
        "nba": {
            "aliases": {},
            "mappings": {},
            "settings": {
                "index": {
                    "creation_date": "1589720812361",
                    "number_of_shards": "1",
                    "number_of_replicas": "1",
                    "uuid": "tVhjYSMvQWmYIznpx_ejnw",
                    "version": {
                        "created": "7070099"
                    },
                    "provided_name": "nba"
                }
            }
        }
    }
    ```
* 获取所有索引

    * 方式一

        ```
        curl -X GET "localhost:9200/_all"
        ```

        ```json
        {
            "cba": {
                "aliases": {},
                "mappings": {},
                "settings": {
                    "index": {
                        "creation_date": "1589720765271",
                        "number_of_shards": "1",
                        "number_of_replicas": "1",
                        "uuid": "fY4nCD0qTB-nxcAzkrvQtg",
                        "version": {
                            "created": "7070099"
                        },
                        "provided_name": "cba"
                    }
                }
            },
            "nba": {
                "aliases": {},
                "mappings": {},
                "settings": {
                    "index": {
                        "creation_date": "1589720812361",
                        "number_of_shards": "1",
                        "number_of_replicas": "1",
                        "uuid": "tVhjYSMvQWmYIznpx_ejnw",
                        "version": {
                            "created": "7070099"
                        },
                        "provided_name": "nba"
                    }
                }
            }
        }
        ```

    * 方式二

        ```
        curl -X GET "localhost:9200/_cat/indices?v"
        ```

        ```
        health status index uuid                   pri rep docs.count docs.deleted store.size pri.store.size
        yellow open   cba   fY4nCD0qTB-nxcAzkrvQtg   1   1          0            0       208b           208b
        yellow open   nba   tVhjYSMvQWmYIznpx_ejnw   1   1          0            0       208b           208b
        ```

* 判断索引是否存在

    ```
    curl -I HEAD "localhost:9200/nba"
    ```

    ```
    200 OK
    ```

    ```
    404 Not Found
    ```

* 关闭索引

    ```
    curl -X POST "localhost:9200/nba/_close"
    ```

    ```json
    {
        "acknowledged": true,
        "shards_acknowledged": true,
        "indices": {
            "nba": {
                "closed": true
            }
        }
    }
    ```

    查询字段时`verified_before_close`为`false`
    
    ```json
    {
        "nba": {
            "aliases": {},
            "mappings": {},
            "settings": {
                "index": {
                    "verified_before_close": "true",
                    "number_of_shards": "1",
                    "provided_name": "nba",
                    "creation_date": "1589720812361",
                    "number_of_replicas": "1",
                    "uuid": "tVhjYSMvQWmYIznpx_ejnw",
                    "version": {
                        "created": "7070099"
                    }
                }
            }
        }
    }
    ```

* 关闭索引

    ```
    curl -X POST "localhost:9200/nba/_open"
    ```

    ```json
    {
        "acknowledged": true,
        "shards_acknowledged": true
    }
    ```

### 映射的介绍和使用


* 获取索引结构

    ```
    curl -X GET "localhost:9200/nba/_mapping"
    ```

    ```json
    {
        "nba": {
            "mappings": {}
        }
    }
    ```

* 新增mapping和新增字段

    ```
    curl -X PUT "localhost:9200/nba/_mapping"
    ```

    * 参数

        指定数据结构中有哪些字段，和字段类型

        ```json
        {
            "properties": {
                "name": {
                    "type": "text"
                },
                "team_name": {
                    "type": "text"
                },
                "position": {
                    "type": "keyword"
                },
                "play_year": {
                    "type": "keyword"
                },
                "jerse_no": {
                    "type": "keyword"
                }
            }
        }
        ```
    * 响应

        ```json
        {
            "acknowledged": true
        }
        ```

* 批量获取索引结构

    ```
    curl -X GET "localhost:9200/nba,cba/_mapping"
    ```

    ```json
    {
        "nba": {
            "mappings": {}
        },
        "cba": {
            "mappings": {}
        }
    }
    ```

* 批量获取所有

    ```
    curl -X GET "localhost:9200/_mapping"
    ```

    ```json
    {
        "cba": {
            "mappings": {}
        },
        "nba": {
            "mappings": {
                "properties": {
                    "jerse_no": {
                        "type": "keyword"
                    },
                    "name": {
                        "type": "text"
                    },
                    "play_year": {
                        "type": "keyword"
                    },
                    "position": {
                        "type": "keyword"
                    },
                    "team_name": {
                        "type": "text"
                    }
                }
            }
        }
    }
    ```

**不能删除索引的mapping的字段，需要重新创建索引**


### 文档的增删改查





