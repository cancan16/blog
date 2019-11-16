---
title: RocketMQ
date: 2019-11-10 18:18:59
update: 2019-11-10 18:18:59
categories: RocketMQ
tags: [RocketMQ]
---

### linux安装rocketmq

* RocketMQ核心组件

    * Broker：MQ程序，接收生产的消息，提供给消费者消费的程序

    * Name Server：给生产和消费者提供路由信息，提供轻量级的服务发现、路由、元数据信息，可以多个部署，互相独立（比zookeeper更轻量）

* 快速开始 <http://rocketmq.apache.org/docs/quick-start/> 下载安装包：<http://mirror.bit.edu.cn/apache/rocketmq/4.4.0/rocketmq-all-4.4.0-source-release.zip>

```
unzip rocketmq-all-4.4.0-source-release.zip
cd rocketmq-all-4.4.0/
mvn --settings /usr/share/apache-maven/conf/settings.xml -Prelease-all -DskipTests clean install -U
cd distribution/target/apache-rocketmq
```

<!-- more -->  

* 最新版本部署存在问题：

  * Please set the JAVA_HOME variable in your environment, We need java(x64)
  * 解决：本地需要配置 JAVA_HOME 使用命令 vim ~/.bash_profile

```
JAVA_HOME="/Library/Java/JavaVirtualMachines/jdk1.8.0_171.jdk/Con
export JAVA_HOME
CLASS_PATH="$JAVA_HOME/lib"
PATH=".$PATH:$JAVA_HOME/bin"
```

  * 启动nameServer

```
nohup sh bin/mqnamesrv &
```
 
   * win环境启动

```sh
start mqnamesrv.cmd
```   

* 启动时会遇到内存不够用的错误，这时需要改变JVM分配给rocketmq的内存

修改`${ROCKETMQ_HOME}/bin/runserver.sh`文件，

```
JAVA_OPT="${JAVA_OPT} -server -Xms4g -Xmx4g -Xmn2g -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"
```

- 查看日志 tail -f nohup.out (结尾：The Name Server boot success. serializeType=JSON 表示启动成功)

- 启动broker (-n指定nameserver地址，nameserver服务端口为9876, broker默认端口 10911)

```
# nohup sh bin/mqbroker -n 192.168.25.11:9876 autoCreateTopicEnable=true &
nohup sh bin/mqbroker -n localhost:9876 autoCreateTopicEnable=true &
```

 * win环境下启动

```sh
start mqbroker.cmd -n localhost:9876 autoCreateTopicEnable=true
```

* 查看Topic列表信息

```sh
# start mqadmin.cmd topicList -n localhost:9876
sh bin/mqadmin topicList -n 192.168.25.11:9876
```

 * 报错

```
OpenJDK 64-Bit Server VM warning: ignoring option PermSize=128m; support was removed in 8.0
OpenJDK 64-Bit Server VM warning: ignoring option MaxPermSize=128m; support was removed in 8.0
org.apache.rocketmq.tools.command.SubCommandException: TopicListSubCommand command failed
```

 * 解决方法

修改`apache-rocketmq/bin/tools.sh`文件
在${JAVA_HOME}/jre/lib/ext后加上ext文件夹的绝对路径
例如：

```sh
JAVA_OPT="${JAVA_OPT} -Djava.ext.dirs=${BASE_DIR}/lib:${JAVA_HOME}/jre/lib/ext::/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.232.b09-0.el7_7.x86_64/jre/lib/ext"
```

 * 重新查看

```
[root@localhost apache-rocketmq]# sh bin/mqadmin topicList -n 192.168.25.11:9876
OpenJDK 64-Bit Server VM warning: ignoring option PermSize=128m; support was removed in 8.0
OpenJDK 64-Bit Server VM warning: ignoring option MaxPermSize=128m; support was removed in 8.0
RMQ_SYS_TRANS_HALF_TOPIC
BenchmarkTest
OFFSET_MOVED_EVENT
TBW102
localhost.localdomain
SELF_TEST_TOPIC
DefaultCluster
```

- 关闭nameserver broker执行的命令

```
sh bin/mqshutdown broker
```

```
sh bin/mqshutdown namesrv
```

### springboot集成rocketmq


#### 引入依赖


```xml
<dependency>
    <groupId>org.apache.rocketmq</groupId>
    <artifactId>rocketmq-client</artifactId>
    <version>4.3.0</version>
</dependency>
```


#### 配置rocketmq地址

```
rocketmq.producer.groupName=shop
rocketmq.producer.namesrvAddr=127.0.0.1:9876
rocketmq.producer.default=true
```

#### 创建rocketmq实例对象

```java
import org.apache.rocketmq.client.exception.MQClientException;
import org.apache.rocketmq.client.producer.DefaultMQProducer;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @Description: rocketmq实例对象
 * @Date: 2019/11/10
 */
@Configuration
public class ProducerConfigure {

    private static final Logger log = LoggerFactory.getLogger(ProducerConfigure.class);

    @Value("${rocketmq.producer.groupName}")
    private String groupName;
    @Value("${rocketmq.producer.namesrvAddr}")
    private String namesrvAddr;

    @Bean
    public DefaultMQProducer defaultMQProducer() throws MQClientException {
        DefaultMQProducer producer = new DefaultMQProducer(groupName);
        producer.setNamesrvAddr(namesrvAddr);
        producer.setRetryTimesWhenSendAsyncFailed(10);
        producer.start();
        log.info("start rocketmq producer success!");
        return producer;
    }
}
```

```java
@GetMapping("/test")
public void test(String info) throws Exception {
    Message message = new Message("BenchmarkTest", "Tag1", "12345", "rocketmq测试成功".getBytes());
    // 这里用到了这个mq的异步处理，类似ajax，可以得到发送到mq的情况，并做相应的处理
    //不过要注意的是这个是异步的
    producer.send(message, new SendCallback() {
        @Override
        public void onSuccess(SendResult sendResult) {
            log.info("传输成功");
            log.info(JSON.toJSONString(sendResult));
        }

        @Override
        public void onException(Throwable e) {
            log.error("传输失败", e);
        }
    });
}
```    
```
2019-11-16 22:32:55.557  INFO 5900 --- [blicExecutor_12] c.x.shop.controller.TestController       : 传输成功
2019-11-16 22:32:55.557  INFO 5900 --- [blicExecutor_12] c.x.shop.controller.TestController       : {"messageQueue":{"brokerName":"PC-201908041232","queueId":831,"topic":"BenchmarkTest"},"msgId":"C0A8026E170C18B4AAC2521608020004","offsetMsgId":"C0A8190100002A9F00000000000003D9","queueOffset":0,"regionId":"DefaultRegion","sendStatus":"SEND_OK","traceOn":true}
```


### 问题

虚拟机安装rocketmq，可以查看到topic有哪些，不管是不是自定义topic，发送消息报错

`tx-mq-TOPIC`为自定义topic

```
org.apache.rocketmq.client.exception.MQClientException: Send [1] times, still failed, cost [3238]ms, Topic: tx-mq-TOPIC, BrokersSent: [localhost.localdomain]
See http://rocketmq.apache.org/docs/faq/ for further details.
```