---
title: 自定义Ribbon策略
date: 2019-05-04 21:53:53
update: 2019-05-04 21:53:53
categories: SPRINGCLOUD
tags: [springcloud]
---

### 自定义Ribbon策略


<a href="http://cloud.spring.io/spring-cloud-static/Finchley.RELEASE/single/spring-cloud.html#_customizing_the_ribbon_client_by_setting_properties">官方文档</a>

```yml
# 自定义本地负载均衡策略为随机策略，默认是轮询
product-service:
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule
```

<!-- more -->

![自定义Ribbon策略](https://volc1612.gitee.io/blog/images/自定义Ribbon策略/自定义Ribbon策略_a.png)

> 策略选择

* 1. 如果每个机器配置一样，则建议不修改策略 (推荐)
* 2. 如果部分机器配置强，则可以改为 WeightedResponseTimeRule