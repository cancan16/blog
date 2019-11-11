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
  mvn -Prelease-all -DskipTests clean install -U
  cd distribution/target/apache-rocketmq
  ```

  

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

- ```
  nohup sh bin/mqnamesrv &
  ```

* 启动时会遇到内存不够用的错误，这时需要改变JVM分配给rocketmq的内存

修改`${ROCKETMQ_HOME}/bin/runserver.sh`文件，

```
JAVA_OPT="${JAVA_OPT} -server -Xms4g -Xmx4g -Xmn2g -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"
```

- 查看日志 tail -f nohup.out (结尾：The Name Server boot success. serializeType=JSON 表示启动成功)

- 启动broker (-n指定nameserver地址，nameserver服务端口为9876, broker默认端口 10911)

  ```
  # nohup sh bin/mqbroker -n 192.168.25.11:9876 &
  nohup sh bin/mqbroker -n localhost:9876 &
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
