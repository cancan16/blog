---
title: Dubbo Admin
date: 2019-10-05 21:26:10
update: 2019-10-05 21:26:10
categories: DUBBO
tags: [dubbo]
---

### 下载dubbo-admin 

```
git clone https://github.com/apache/dubbo-admin.git
```

### 配置zookepper地址

```powershell
cd F:\wk\tool\dubbo-admin\dubbo-admin-server\src\main\resources
```

```yml
admin.registry.address=zookeeper://192.168.25.11:2181
admin.config-center=zookeeper://192.168.25.11:2181
admin.metadata-report.address=zookeeper://192.168.25.11:2181
```

### build编译

```powershell
mvn clean package
```

### 启动

```powershell
cd dubbo-admin-distribution/target
nohup java -jar dubbo-admin-0.1.jar &
```

### Visit `http://localhost:8080`