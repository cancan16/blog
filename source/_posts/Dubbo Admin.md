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

#### 编译报错

```sh
[INFO] Running 'npm install' in F:\wk\tool\dubbo-admin\dubbo-admin-ui
[ERROR] internal/modules/cjs/loader.js:550
[ERROR]     throw err;
[ERROR]     ^
[ERROR]
[ERROR] Error: Cannot find module '../lib/utils/unsupported.js'
[ERROR]     at Function.Module._resolveFilename (internal/modules/cjs/loader.js:548:15)
[ERROR]     at Function.Module._load (internal/modules/cjs/loader.js:475:25)
[ERROR]     at Module.require (internal/modules/cjs/loader.js:598:17)
[ERROR]     at require (internal/modules/cjs/helpers.js:11:18)
[ERROR]     at F:\wk\tool\dubbo-admin\dubbo-admin-ui\node\node_modules\npm\bin\npm-cli.js:19:21
[ERROR]     at Object.<anonymous> (F:\wk\tool\dubbo-admin\dubbo-admin-ui\node\node_modules\npm\bin\npm-cli.js:92:3)
[ERROR]     at Module._compile (internal/modules/cjs/loader.js:654:30)
[ERROR]     at Object.Module._extensions..js (internal/modules/cjs/loader.js:665:10)
[ERROR]     at Module.load (internal/modules/cjs/loader.js:566:32)
[ERROR]     at tryModuleLoad (internal/modules/cjs/loader.js:506:12)
```

解决：

1. 下载安装`nodejs`，安装成功后将`E:\program\nodejs\node_modules`的`node_modules`文件夹复制替换`F:\wk\tool\dubbo-admin\dubbo-admin-ui\node`中的node_modules。
2. 将duboo-amdin项目导入到IDEA中，使用IDEA clean 和 package

### 启动

```powershell
cd dubbo-admin-distribution/target
nohup java -jar dubbo-admin-0.1.jar &
```

### Visit

`http://localhost:8080`

![dubbo-admin-a](https://volc1612.gitee.io/blog/images/dubbo-admin/dubbo-admin-a.png)