---
title: 解析json字符串
date: 2017-05-16 23:21:00
update: 2017-06-1 23:21:00
categories: JAVA
tags: [fastjson,jackson]
---
### 1 使用fastjson解析json字符串

<!-- more -->

要解析的json串
```
{				--首先创建DatasResult类用于接收数据
  "data": [			--可以看出data这个元素属于一个集合，所以在DataResult类中创建List<Data>集合属性
    {				--List<Data>集合包含很多Data类型，创建Data类，Date类中有title属性和result类
      "title": "123",
      "result": {		--而result元素又属于一个对象，在Data类中创建DataResult类
        "name": "volc"		--DataResult类中包含name属性
      }
    },
    {
      "title": "123",
      "result": {
        "name": "volc"
      }
    }
  ]
}
```


1.构造实体类

创建DatasResult的实体类用于接受数据
里面包含List<Data>集合属性

```java
import java.util.ArrayList;
import java.util.List;

public class DatasResult {
	List<Data> data = new ArrayList<Data>();

	public List<Data> getData() {
		return data;
	}
	public void setData(List<Data> data) {
		this.data = data;
	}
}
```

创建Data类型，里面包含title属性和result类，在Date类型创建DataResult类，DataResult类中包含name属性
```java
public class Data {

	private String title;
	private DataResult result;

	public class DataResult {
		private String name;

		public String getName() {
			return name;
		}

		public void setName(String name) {
			this.name = name;
		}
	}

	public DataResult getResult() {
		return result;
	}

	public void setResult(DataResult result) {
		this.result = result;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	@Override
	public String toString() {
		return "Data [title=" + title + ", result=" + result + "]";
	}
}
```
2 解析
```java
public class JSONToList {
	public static void main(String[] args) {
		String json = "{\"data\":[{\"title\":\"123\",\"result\":{\"name\":\"volc\"}},{\"title\":\"123\",\"result\":{\"name\":\"volc\"}}]}";
		System.out.println(json);
		DatasResult parseObject = JSON.parseObject(json, DatasResult.class);
		System.out.println(parseObject.getData().get(0).getResult().getName());
	}
}
```
### 2 使用jackson解析

```
依赖
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.8.7</version>
</dependency>
```

DataResult类
```java

import java.util.List;
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;

public class DatasResult {

    // 定义jackson对象
    private static final ObjectMapper MAPPER = new ObjectMapper();

    private List<?> data;

    public DatasResult() {
    }

    public DatasResult(List<?> data) {
	this.data = data;
    }

    public List<?> getData() {
	return data;
    }

    public void setData(List<?> data) {
	this.data = data;
    }

    /**
     * Object是集合转化
     * 
     * @param jsonData json数据
     * @param clazz 集合中的类型
     * @return
     */
    public static DatasResult formatToList(String jsonData, Class<?> clazz) {
	try {
	    JsonNode jsonNode = MAPPER.readTree(jsonData);
	    JsonNode data = jsonNode.get("data");
	    List<?> list = null;
	    if (data.isArray() && data.size() > 0) {
		list = MAPPER.readValue(data.traverse(), MAPPER.getTypeFactory().constructCollectionType(List.class, clazz));
	    }
	    return new DatasResult(list);
	} catch (Exception e) {
	    return null;
	}
    }
}
```
Data类
```java
public class Data {
    private String title;
    private DataResult result;

    public class DataResult {
	private String name;

	public String getName() {
	    return name;
	}

	public void setName(String name) {
	    this.name = name;
	}
    }

    public DataResult getResult() {
	return result;
    }

    public void setResult(DataResult result) {
	this.result = result;
    }

    public String getTitle() {
	return title;
    }

    public void setTitle(String title) {
	this.title = title;
    }

    @Override
    public String toString() {
	return "Data [title=" + title + ", result=" + result + "]";
    }
}
```
```java
/**
 * @ClassName Jackson
 * @Author volc
 * @Description 解析json字符串节点为对象
 * @Date 2017年6月2日 上午1:51:26
 */
public class Jackson {

    public static void main(String[] args) {
	String json = "{\"data\":[{\"title\":\"123\",\"result\":{\"name\":\"volc\"}},{\"title\":\"123\",\"result\":{\"name\":\"volc\"}}]}";
	DatasResult dataResult = DatasResult.formatToList(json, Data.class);
	System.out.println(dataResult);
    }

}
```
### 3 使用jackson解析json指定节点

```java
import java.io.IOException;
import java.util.ArrayList;
import java.util.LinkedHashMap;
import java.util.List;
import java.util.Map;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.node.ArrayNode;

/**
 * @ClassName Jackson
 * @Author volc
 * @Description 解析json字符串中的节点对象
 * @Date 2017年6月2日 上午1:51:26
 */
public class Jackson2 {
    private static final ObjectMapper MAPPER = new ObjectMapper();

    public static void main(String[] args) throws JsonProcessingException, IOException {
	String json = "{\"data\":[{\"title\":\"123\",\"result\":{\"name\":\"volc\"}},{\"title\":\"123\",\"result\":{\"name\":\"volc\"}}]}";
	// 解析json生成前端所需要的json数据
	JsonNode jsonNode = MAPPER.readTree(json);
	ArrayNode arrayNode = (ArrayNode) jsonNode.get("data");
	// 定义含有map集合的list用于组织封装想要的格式
	List<Map<String, Object>> result = new ArrayList<Map<String, Object>>();
	for (JsonNode data : arrayNode) {
	    // 有顺序的hashmap
	    Map<String, Object> map = new LinkedHashMap<String, Object>();
	    map.put("result", data.get("result").get("name").asText()); // 获取节点对象并获取值
	    result.add(map);
	}
	System.out.println(MAPPER.writeValueAsString(result));
    }
}

```
### 4 jackson解析为list

```
[
  {
    "created": 1488441787000,
    "updated": 1488441787000,
    "id": 1474391928,
    "title": "电子书测试",
    "sellPoint": "电子书测试",
    "price": 1200,
    "num": 1111,
    "barcode": "",
    "image": "http://image.taotao.com/images/2017/03/02/2017030204021882807073.jpg,http://image.taotao.com/images/2017/03/02/2017030204021910209674.jpg",
    "cid": 3,
    "status": 1
  },
  {
    "created": 1425821628000,
    "updated": 1488441643000,
    "id": 1474391927,
    "title": "6小米（MI）小米米键 颜色随机 官方正品",
    "sellPoint": "智键手机快捷键防尘塞",
    "price": 100,
    "num": 99999,
    "barcode": "",
    "image": "http://image.taotao.com/jd/0a182402ff97495f882f20244c71284b.jpg",
    "cid": 560,
    "status": 1
  },
  {
    "created": 1425821628000,
    "updated": 1425821628000,
    "id": 1473655354,
    "title": "小米 红米Note 8G内存移动4G合约增强版 不含合约计划 白色 移动4G",
    "sellPoint": "",
    "price": 1159000,
    "num": 99999,
    "barcode": null,
    "image": "http://image.taotao.com/jd/26cc33af58e3452aa27a207db7c35a1e.jpg",
    "cid": 560,
    "status": 1
  }
]
```
bean对象Item类
```java

import org.apache.commons.lang3.StringUtils;
import org.apache.solr.client.solrj.beans.Field;
import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@JsonIgnoreProperties(ignoreUnknown = true)
/*
 * 忽略为知的字段,因为以下字段是为写入到solr用的，用jackson反序列化时需要，反序列化以下字段即可，不需要加入到solr的字段我们需要忽略.
 * 否则在反序列化时会报错
 * com.fasterxml.jackson.databind.exc.UnrecognizedPropertyException: Unrecognized field "created" (class cn.itcast.solrj.pojo.Item), not marked as ignorable (8 known properties: "status", "title", "price", "id", "updated", "image", "sellPoint", "cid"])
 */

public class Item {

    @JsonProperty("id")
    private Long id;

    @JsonProperty("title")
    private String title;

    @JsonProperty("sellPoint")
    private String sellPoint;

    @JsonProperty("price")
    private Long price;

    @JsonProperty("image")
    private String image;

    @JsonProperty("cid")
    private Long cid;

    @JsonProperty("status")
    private Integer status;

    @JsonProperty("updated")
    private Long updated;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getSellPoint() {
        return sellPoint;
    }

    public void setSellPoint(String sellPoint) {
        this.sellPoint = sellPoint;
    }

    public Long getPrice() {
        return price;
    }

    public void setPrice(Long price) {
        this.price = price;
    }

    public String getImage() {
        return image;
    }

    public void setImage(String image) {
        this.image = image;
    }

    public Long getCid() {
        return cid;
    }

    public void setCid(Long cid) {
        this.cid = cid;
    }

    public Integer getStatus() {
        return status;
    }

    public void setStatus(Integer status) {
        this.status = status;
    }

    public Long getUpdated() {
        return updated;
    }

    public void setUpdated(Long updated) {
        this.updated = updated;
    }

    public String[] getImages() {
        return StringUtils.split(this.getImage(), ',');
    }

    @Override
    public String toString() {
        return "Item [id=" + id + ", title=" + title + ", sellPoint=" + sellPoint + ", price=" + price
                + ", image=" + image + ", cid=" + cid + ", status=" + status + ", updated=" + updated + "]";
    }

}

```
解析代码
```java
String jsonData = doGet(u);
JsonNode jsonNode = MAPPER.readTree(jsonData);
String rowsStr = jsonNode.get("rows").toString(); // rowsStr字符串就是上面要解析为List的资源
List<Item> items = MAPPER.readValue(rowsStr, MAPPER.getTypeFactory().constructCollectionType(List.class, Item.class));
```
#### 4.1 jackson解析字符串异常

```
Exception in thread "main" com.fasterxml.jackson.databind.exc.UnrecognizedPropertyException: Unrecognized field "created" (class com.biusoft.Item), not marked as ignorable (8 known properties: , "status", "title", "price", "id", "updated", "image", "sellPoint", "cid"])
```

需要在接受实体类上添加@JsonIgnoreProperties(ignoreUnknown = true)，忽略未知字段的解析。

### jackson 添加list节点(添加节点转对象)

```java
import com.biusoft.core.common.config.Global;
import com.biusoft.core.common.utils.ResponseInfo;
import com.biusoft.core.modules.zhxc.dao.XunCJLDao;
import com.biusoft.core.modules.zhxc.entity.*;
import com.biusoft.core.modules.zhxc.utils.HttpClientUtils;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.node.ArrayNode;
import com.fasterxml.jackson.databind.node.JsonNodeFactory;
import com.fasterxml.jackson.databind.node.ObjectNode;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.util.List;
import java.util.Map;

/**
 * @Author: volc
 * @Description: 登录操作
 * @Date: 20:26 2018/1/11
 */

@Service
public class DengLuService {

    private static final ObjectMapper MAPPER = new ObjectMapper();

    @Autowired
    private XunCJLDao xunCJLDao;

    /**
     * 用户信息
     *
     * @param param
     * @return
     */
    public ResponseInfo dengLuInfo(Map<String, Object> param) throws Exception {
        ResponseInfo responseInfo = new ResponseInfo();
        String userInfo = HttpClientUtils.requestByGetMethod(Global.getConfig("user.info"), param); // 用户登录状态及用户信息
        if (null != userInfo && "false".equals(MAPPER.readTree(userInfo).get("success").asText())) {
            responseInfo.setSuccess(false);
            responseInfo.setInfo("登录失败");
        } else if (null != userInfo && "true".equals(MAPPER.readTree(userInfo).get("success").asText())) { // 登录成功
            // 获取msg节点，
            ObjectNode msgNode = (ObjectNode) MAPPER.readTree(userInfo).get("msg");
            // 查询未完成的任务记录id
            List<XunCJL> xuncJL = xunCJLDao.getXuncJL(msgNode.get("id").asText());
            JsonNodeFactory factory = new JsonNodeFactory(false);
            ObjectNode xuncJLNode = factory.objectNode();
            xuncJLNode.putAll(msgNode);
			// 创建一个数组节点
            ArrayNode xuncJLNodeArray = factory.arrayNode();
			// 组织数组
            for (XunCJL xunCJL : xuncJL) {
                xuncJLNodeArray.addPOJO(xunCJL);
            }
			// 添加数组节点
            xuncJLNode.set("xuncJl", xuncJLNodeArray);
            responseInfo.setInfo("登录成功");
            responseInfo.setMsg(xuncJLNode);
            responseInfo.setSuccess(true);
        }
        return responseInfo;
    }
}

```

### jackson序列化日期

实体字段上添加
@JsonFormat(pattern = "yyyy-MM-dd")

### 反序列化

@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
private Date startTime;
