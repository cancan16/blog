---
title: 简单使用MongDB-GEO
date: 2019-04-18 15:39:17
update: 2019-04-18 15:39:17
categories: MongDB
tags: [mongdb]
---

### win安装mongodb

MongDB版本
<a href="https://www.mongodb.com/dr/fastdl.mongodb.org/win32/mongodb-win32-x86_64-2008plus-ssl-3.6.12.zip/download">3.6.12下载地址</a>

* 创建`mongo.conf`配置文件，内容如下

```
dbpath=D:\mongo\data
logpath=D:\mongo\log\mongo.log
logappend=true
journal=true
quiet=true
port=27017
```

* 启动MongDb

```bash
D:\programs\mongodb-win32-x86_64-2008plus-ssl-3.6.12\bin>mongod.exe --config D:\mongo\config\mongo.conf
```

### JPA添加数据和使用GEO索引查询中心点附近数据

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
<dependency>
    <groupId>org.mongodb</groupId>
    <artifactId>mongo-java-driver</artifactId>
    <version>3.4.3</version>
</dependency>
```

```yml
##mongo配置
spring:
  data:
    mongodb:
      host: 127.0.0.1
      port: 27017
      database: test
```

实体类

```java
import lombok.Data;
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.index.GeoSpatialIndexType;
import org.springframework.data.mongodb.core.index.GeoSpatialIndexed;
import org.springframework.data.mongodb.core.index.Indexed;
import org.springframework.data.mongodb.core.mapping.Document;

@Data
@Document(collection = "bikes")
public class Bike {
    @Id
    private String id;
    private int status;
    @Indexed
    private long bikeNo;
    // 空间球面索引
    @GeoSpatialIndexed(type = GeoSpatialIndexType.GEO_2DSPHERE)
    private double[] location;
}
```

```java
import com.niubike.niubike.entity.Bike;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.geo.GeoResult;
import org.springframework.data.geo.GeoResults;
import org.springframework.data.geo.Metrics;
import org.springframework.data.mongodb.core.MongoTemplate;
import org.springframework.data.mongodb.core.query.Criteria;
import org.springframework.data.mongodb.core.query.NearQuery;
import org.springframework.data.mongodb.core.query.Query;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
public class BikeController {

    @Autowired
    private MongoTemplate mongoTemplate;

    @PostMapping("/addBike")
    public String save(@RequestBody Bike bike) {
        bike.setBikeNo(System.currentTimeMillis());
        bike.setStatus(0);
        mongoTemplate.insert(bike);
        return "1";
    }

    /**
     * 根据当前经纬度查找附近单车
     *
     * @return
     */
    @PostMapping("/findNearBike")
    public List<GeoResult<Bike>> findNearBike(double longitude, double latitude) {
        // 200米范围内，状态为的0的bike， 数量限制20
        NearQuery nearQuery = NearQuery
                .near(longitude, latitude)
                .maxDistance(0.2, Metrics.KILOMETERS)
                .query(new Query(Criteria.where("status").is(0)).limit(20));
        GeoResults<Bike> geoResults = mongoTemplate.geoNear(nearQuery, Bike.class);
        return geoResults.getContent();
    }
}
```

a.png


b.png





