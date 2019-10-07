---
title: 基于JVM的ShutdownHook优雅关闭linux进程
date: 2019-10-07 16:07:20
update: 2019-10-07 16:07:20
categories: LINUX
tags: [linux]
---


<!-- more -->

### 使用`kill -15 pid`

```java
import org.apache.dubbo.config.spring.context.annotation.DubboComponentScan;
import org.apache.dubbo.config.spring.context.annotation.EnableDubboConfig;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@EnableDubboConfig
@DubboComponentScan("com.xdclass.userapp.service.dubbo")
@MapperScan("com.xdclass.userapp.mapper")
public class UserAppApplication {

    public static void main(String[] args) {
        SpringApplication.run(UserAppApplication.class, args);
         /***
         * jvm的shutdownHook
         */
        Runtime.getRuntime().addShutdownHook(new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("执行JVM ShutdownHook！！");
            }
        }));
    }

}

```

```java
import org.springframework.beans.factory.DisposableBean;
import org.springframework.stereotype.Component;

/**
 * @Author: Liu, cancan
 * @Description: 测试spring自带的hook DisposableBean 项目关闭时触发
 * @Date: 2019/10/7
 */
@Component
public class TestDisposeableBean implements DisposableBean {

    /**
     * @Description:
     * @Param: []
     * @return: void
     * @Date: 2019/10/7
     */
    @Override
    public void destroy() throws Exception {
        System.out.println("测试TestDisposeableBean已经销毁");
    }
}
```

查找占用端口号的进程

```sh
[root@localhost project]# lsof -i:8092
COMMAND   PID USER   FD   TYPE   DEVICE SIZE/OFF NODE NAME
java    87208 root   47u  IPv6 19336846      0t0  TCP *:8092 (LISTEN)
[root@localhost project]# kill -15 87208
[root@localhost project]# 
```

服务关闭时日志

```
2019-10-07 16:01:43.838  INFO 87208 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8092 (http) with context path ''
2019-10-07 16:01:43.840  INFO 87208 --- [           main] com.xdclass.userapp.UserAppApplication   : Started UserAppApplication in 9.836 seconds (JVM running for 10.857)
执行JVM ShutdownHook！！
2019-10-07 16:03:22  INFO AbstractRegistryFactory:68 -  [DUBBO] Close all registries [zookeeper://192.168.25.11:2181/org.apache.dubbo.registry.RegistryService?application=user-app&dubbo=2.0.2&interface=org.apache.dubbo.registry.RegistryService&pid=87208&release=2.7.2&timestamp=1570435303054], dubbo version: 2.7.2, current host: 192.168.25.11
2019-10-07 16:03:22  INFO ZookeeperRegistry:458 -  [DUBBO] Destroy registry:zookeeper://192.168.25.11:2181/org.apache.dubbo.registry.RegistryService?application=user-app&dubbo=2.0.2&interface=org.apache.dubbo.registry.RegistryService&pid=87208&release=2.7.2&timestamp=1570435303054, dubbo version: 2.7.2, current host: 192.168.25.11
2019-10-07 16:03:22  INFO ZookeeperRegistry:291 -  [DUBBO] Unregister: dubbo://192.168.25.11:20880/com.xdclass.userapi.service.IUserService?accesslog=dubbo-access.log&anyhost=true&application=user-app&bean.name=ServiceBean:com.xdclass.userapi.service.IUserService&deprecated=false&dubbo=2.0.2&dynamic=true&generic=false&interface=com.xdclass.userapi.service.IUserService&methods=getUserById&pid=87208&register=true&release=2.7.2&retries=0&revision=0.0.1-SNAPSHOT&side=provider&timeout=3000&timestamp=1570435303055, dubbo version: 2.7.2, current host: 192.168.25.11
2019-10-07 16:03:22  INFO ZookeeperRegistry:467 -  [DUBBO] Destroy unregister url dubbo://192.168.25.11:20880/com.xdclass.userapi.service.IUserService?accesslog=dubbo-access.log&anyhost=true&application=user-app&bean.name=ServiceBean:com.xdclass.userapi.service.IUserService&deprecated=false&dubbo=2.0.2&dynamic=true&generic=false&interface=com.xdclass.userapi.service.IUserService&methods=getUserById&pid=87208&register=true&release=2.7.2&retries=0&revision=0.0.1-SNAPSHOT&side=provider&timeout=3000&timestamp=1570435303055, dubbo version: 2.7.2, current host: 192.168.25.11
2019-10-07 16:03:22  INFO ZookeeperRegistry:320 -  [DUBBO] Unsubscribe: provider://192.168.25.11:20880/com.xdclass.userapi.service.IUserService?accesslog=dubbo-access.log&anyhost=true&application=user-app&bean.name=ServiceBean:com.xdclass.userapi.service.IUserService&bind.ip=192.168.25.11&bind.port=20880&category=configurators&check=false&deprecated=false&dubbo=2.0.2&dynamic=true&generic=false&interface=com.xdclass.userapi.service.IUserService&methods=getUserById&monitor=dubbo%3A%2F%2F192.168.25.11%3A2181%2Forg.apache.dubbo.registry.RegistryService%3Fapplication%3Duser-app%26dubbo%3D2.0.2%26pid%3D87208%26protocol%3Dregistry%26refer%3Dapplication%253Duser-app%2526dubbo%253D2.0.2%2526interface%253Dorg.apache.dubbo.monitor.MonitorService%2526pid%253D87208%2526register.ip%253D192.168.25.11%2526release%253D2.7.2%2526timestamp%253D1570435303253%26registry%3Dzookeeper%26release%3D2.7.2%26timestamp%3D1570435303054&pid=87208&register=true&release=2.7.2&retries=0&revision=0.0.1-SNAPSHOT&side=provider&timeout=3000&timestamp=1570435303055, dubbo version: 2.7.2, current host: 192.168.25.11
2019-10-07 16:03:22  INFO ZookeeperRegistry:483 -  [DUBBO] Destroy unsubscribe url provider://192.168.25.11:20880/com.xdclass.userapi.service.IUserService?accesslog=dubbo-access.log&anyhost=true&application=user-app&bean.name=ServiceBean:com.xdclass.userapi.service.IUserService&bind.ip=192.168.25.11&bind.port=20880&category=configurators&check=false&deprecated=false&dubbo=2.0.2&dynamic=true&generic=false&interface=com.xdclass.userapi.service.IUserService&methods=getUserById&monitor=dubbo%3A%2F%2F192.168.25.11%3A2181%2Forg.apache.dubbo.registry.RegistryService%3Fapplication%3Duser-app%26dubbo%3D2.0.2%26pid%3D87208%26protocol%3Dregistry%26refer%3Dapplication%253Duser-app%2526dubbo%253D2.0.2%2526interface%253Dorg.apache.dubbo.monitor.MonitorService%2526pid%253D87208%2526register.ip%253D192.168.25.11%2526release%253D2.7.2%2526timestamp%253D1570435303253%26registry%3Dzookeeper%26release%3D2.7.2%26timestamp%3D1570435303054&pid=87208&register=true&release=2.7.2&retries=0&revision=0.0.1-SNAPSHOT&side=provider&timeout=3000&timestamp=1570435303055, dubbo version: 2.7.2, current host: 192.168.25.11
2019-10-07 16:03:22.433  INFO 87208 --- [tor-Framework-0] o.a.c.f.imps.CuratorFrameworkImpl        : backgroundOperationsLoop exiting
2019-10-07 16:03:22  WARN AbstractServer:213 -  [DUBBO] All clients has disconnected from 0.0.0.0/0.0.0.0:20880. You can graceful shutdown now., dubbo version: 2.7.2, current host: 192.168.25.11
2019-10-07 16:03:22.449  INFO 87208 --- [       Thread-2] org.apache.zookeeper.ZooKeeper           : Session: 0x10000004d710032 closed
2019-10-07 16:03:22.449  INFO 87208 --- [ain-EventThread] org.apache.zookeeper.ClientCnxn          : EventThread shut down for session: 0x10000004d710032
2019-10-07 16:03:22  INFO DubboProtocol:616 -  [DUBBO] Close dubbo server: /192.168.25.11:20880, dubbo version: 2.7.2, current host: 192.168.25.11
2019-10-07 16:03:22  INFO AbstractServer:153 -  [DUBBO] Close NettyServer bind /0.0.0.0:20880, export /192.168.25.11:20880, dubbo version: 2.7.2, current host: 192.168.25.11
2019-10-07 16:03:22  INFO DubboProtocol:79 -  [DUBBO] Unexport service: dubbo://192.168.25.11:20880/com.xdclass.userapi.service.IUserService?accesslog=dubbo-access.log&anyhost=true&application=user-app&bean.name=ServiceBean:com.xdclass.userapi.service.IUserService&bind.ip=192.168.25.11&bind.port=20880&deprecated=false&dubbo=2.0.2&dynamic=true&generic=false&interface=com.xdclass.userapi.service.IUserService&methods=getUserById&monitor=dubbo%3A%2F%2F192.168.25.11%3A2181%2Forg.apache.dubbo.registry.RegistryService%3Fapplication%3Duser-app%26dubbo%3D2.0.2%26pid%3D87208%26protocol%3Dregistry%26refer%3Dapplication%253Duser-app%2526dubbo%253D2.0.2%2526interface%253Dorg.apache.dubbo.monitor.MonitorService%2526pid%253D87208%2526register.ip%253D192.168.25.11%2526release%253D2.7.2%2526timestamp%253D1570435303253%26registry%3Dzookeeper%26release%3D2.7.2%26timestamp%3D1570435303054&pid=87208&register=true&release=2.7.2&retries=0&revision=0.0.1-SNAPSHOT&side=provider&timeout=3000&timestamp=1570435303055, dubbo version: 2.7.2, current host: 192.168.25.11
2019-10-07 16:03:22  INFO Server:112 -  [DUBBO] qos-server stopped., dubbo version: 2.7.2, current host: 192.168.25.11
2019-10-07 16:03:22  INFO InjvmProtocol:79 -  [DUBBO] Unexport service: injvm://127.0.0.1/com.xdclass.userapi.service.IUserService?accesslog=dubbo-access.log&anyhost=true&application=user-app&bean.name=ServiceBean:com.xdclass.userapi.service.IUserService&bind.ip=192.168.25.11&bind.port=20880&deprecated=false&dubbo=2.0.2&dynamic=true&generic=false&interface=com.xdclass.userapi.service.IUserService&methods=getUserById&pid=87208&register=true&release=2.7.2&retries=0&revision=0.0.1-SNAPSHOT&side=provider&timeout=3000&timestamp=1570435303055, dubbo version: 2.7.2, current host: 192.168.25.11
2019-10-07 16:03:22  INFO ZookeeperRegistry:212 -  [DUBBO] Load registry cache file /root/.dubbo/dubbo-registry-user-app-192.168.25.11:2181.cache, data: {com.xdclass.userapi.service.IUserService=empty://192.168.25.11:20880/com.xdclass.userapi.service.IUserService?accesslog=dubbo-access.log&anyhost=true&application=user-app&bean.name=ServiceBean:com.xdclass.userapi.service.IUserService&bind.ip=192.168.25.11&bind.port=20880&category=configurators&check=false&deprecated=false&dubbo=2.0.2&dynamic=true&generic=false&interface=com.xdclass.userapi.service.IUserService&methods=getUserById&monitor=dubbo%3A%2F%2F192.168.25.11%3A2181%2Forg.apache.dubbo.registry.RegistryService%3Fapplication%3Duser-app%26dubbo%3D2.0.2%26pid%3D87208%26protocol%3Dregistry%26refer%3Dapplication%253Duser-app%2526dubbo%253D2.0.2%2526interface%253Dorg.apache.dubbo.monitor.MonitorService%2526pid%253D87208%2526register.ip%253D192.168.25.11%2526release%253D2.7.2%2526timestamp%253D1570435303253%26registry%3Dzookeeper%26release%3D2.7.2%26timestamp%3D1570435303054&pid=87208&register=true&release=2.7.2&retries=0&revision=0.0.1-SNAPSHOT&side=provider&timeout=3000&timestamp=1570435303055}, dubbo version: 2.7.2, current host: 192.168.25.11
2019-10-07 16:03:22.538  INFO 87208 --- [       Thread-2] o.a.c.f.imps.CuratorFrameworkImpl        : Starting
2019-10-07 16:03:22.539  INFO 87208 --- [       Thread-2] org.apache.zookeeper.ZooKeeper           : Initiating client connection, connectString=192.168.25.11:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@20bb596e
2019-10-07 16:03:22.560  INFO 87208 --- [       Thread-2] o.a.c.f.imps.CuratorFrameworkImpl        : Default schema
2019-10-07 16:03:22.583  INFO 87208 --- [168.25.11:2181)] org.apache.zookeeper.ClientCnxn          : Opening socket connection to server 192.168.25.11/192.168.25.11:2181. Will not attempt to authenticate using SASL (unknown error)
2019-10-07 16:03:22.583  INFO 87208 --- [168.25.11:2181)] org.apache.zookeeper.ClientCnxn          : Socket connection established to 192.168.25.11/192.168.25.11:2181, initiating session
2019-10-07 16:03:22.585  INFO 87208 --- [168.25.11:2181)] org.apache.zookeeper.ClientCnxn          : Session establishment complete on server 192.168.25.11/192.168.25.11:2181, sessionid = 0x10000004d710033, negotiated timeout = 40000
2019-10-07 16:03:22.585  INFO 87208 --- [d-2-EventThread] o.a.c.f.state.ConnectionStateManager     : State change: CONNECTED
2019-10-07 16:03:22  INFO ZookeeperTransporter:69 -  [DUBBO] No valid zookeeper client found from cache, therefore create a new client for url. zookeeper://192.168.25.11:2181/org.apache.dubbo.registry.RegistryService?application=user-app&dubbo=2.0.2&interface=org.apache.dubbo.registry.RegistryService&pid=87208&release=2.7.2&timestamp=1570435303054, dubbo version: 2.7.2, current host: 192.168.25.11
2019-10-07 16:03:22  INFO ZookeeperRegistry:291 -  [DUBBO] Unregister: dubbo://192.168.25.11:20880/com.xdclass.userapi.service.IUserService?accesslog=dubbo-access.log&anyhost=true&application=user-app&bean.name=ServiceBean:com.xdclass.userapi.service.IUserService&deprecated=false&dubbo=2.0.2&dynamic=true&generic=false&interface=com.xdclass.userapi.service.IUserService&methods=getUserById&pid=87208&register=true&release=2.7.2&retries=0&revision=0.0.1-SNAPSHOT&side=provider&timeout=3000&timestamp=1570435303055, dubbo version: 2.7.2, current host: 192.168.25.11
2019-10-07 16:03:22  INFO ZookeeperRegistry:320 -  [DUBBO] Unsubscribe: provider://192.168.25.11:20880/com.xdclass.userapi.service.IUserService?accesslog=dubbo-access.log&anyhost=true&application=user-app&bean.name=ServiceBean:com.xdclass.userapi.service.IUserService&bind.ip=192.168.25.11&bind.port=20880&category=configurators&check=false&deprecated=false&dubbo=2.0.2&dynamic=true&generic=false&interface=com.xdclass.userapi.service.IUserService&methods=getUserById&monitor=dubbo%3A%2F%2F192.168.25.11%3A2181%2Forg.apache.dubbo.registry.RegistryService%3Fapplication%3Duser-app%26dubbo%3D2.0.2%26pid%3D87208%26protocol%3Dregistry%26refer%3Dapplication%253Duser-app%2526dubbo%253D2.0.2%2526interface%253Dorg.apache.dubbo.monitor.MonitorService%2526pid%253D87208%2526register.ip%253D192.168.25.11%2526release%253D2.7.2%2526timestamp%253D1570435303253%26registry%3Dzookeeper%26release%3D2.7.2%26timestamp%3D1570435303054&pid=87208&register=true&release=2.7.2&retries=0&revision=0.0.1-SNAPSHOT&side=provider&timeout=3000&timestamp=1570435303055, dubbo version: 2.7.2, current host: 192.168.25.11
2019-10-07 16:03:22.635  INFO 87208 --- [       Thread-2] o.s.s.concurrent.ThreadPoolTaskExecutor  : Shutting down ExecutorService 'applicationTaskExecutor'
测试TestDisposeableBean已经销毁
2019-10-07 16:03:22.636  INFO 87208 --- [       Thread-2] f.a.ReferenceAnnotationBeanPostProcessor : class org.apache.dubbo.config.spring.beans.factory.annotation.ReferenceAnnotationBeanPostProcessor was destroying!
2019-10-07 16:03:22  INFO RegistryProtocol:689 -  [DUBBO] Waiting 10000ms for registry to notify all consumers before unexport. Usually, this is called when you use dubbo API, dubbo version: 2.7.2, current host: 192.168.25.11
```

日志打印出了`执行JVM ShutdownHook！！`和`测试TestDisposeableBean已经销毁`。
在执行spring的shutdownhook之前也执行了`ZookeeperRegistr`的shutdownHook，这说明了zookepper也是实用spring的shutdownHook。