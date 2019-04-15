---
title: Jenkins
date: 2019-04-02 21:28:38
update: 2019-04-02 21:28:38
categories: JENKINS
tags: jenkins
---

### 安装Jenkins发布maven项目

#### 创建自由风格项目配置

###### 源码原理

<!-- more -->

![a](https://volc1612.gitee.io/blog/images/jenkins/a.png)

###### 构建

![b](https://volc1612.gitee.io/blog/images/jenkins/b.png)

shell命令

```bash
#!/bin/bash
cd /home/project/volc
./stop.sh
./replace.sh
echo "Execute shell Finish"
BUILD_ID=dontKillMe nohup /home/project/volc/startup.sh &
```

stop.sh

```bash
echo "Stopping volc"
pid=`ps -ef | grep volc-1.0-SNAPSHOT.jar | grep -v grep | awk '{print $2}'`
if [ -n "$pid" ]
then
   echo "kill -9 的pid:" $pid
   kill -9 $pid
fi
```

replace.sh

```bash
cp /var/lib/jenkins/workspace/volc/target/volc-1.0-SNAPSHOT.jar /home/project/volc
```

startup.sh

```bash
java -jar /home/project/volc/volc-1.0-SNAPSHOT.jar
```