---
title: goeasy实时web端推送
date: 2017-08-18 05:24:18
update: 2017-08-18 05:24:18
categories: PUSH
tags: [push]
---

### 1 goeasy实时web端推送
兼容IE浏览器
原理：用户登录系统(客户端)和服务端(推送端)建立websocket连接，服务端推送消息，客户端实时接收到，客户端不在线则接收不到。
<!-- more -->
#### 1.1 注册需要推送的应用客户端
##### 1.1.1 注册goeasy账号
##### 1.1.2 创建应用
![测试](https://volc1612.gitee.io/blog/images/goeasy/app.png)

### 1.2 服务端
引入依赖
```
<dependency>
	<groupId>io.goeasy</groupId>
	<artifactId>goeasy-sdk</artifactId>
	<version>0.3.1</version>
</dependency>
```

使用api方式向web端实时推送
```java
import com.baomidou.framework.annotations.Log;
import com.baomidou.springwind.common.enums.ServerParam;
import com.baomidou.springwind.entity.push.PushContent;
import com.baomidou.springwind.entity.push.Record;
import com.baomidou.springwind.entity.push.Server;
import com.baomidou.springwind.mapper.IPush.PushMapper;
import com.baomidou.springwind.mapper.IPush.RecordMapper;
import com.baomidou.springwind.mapper.IPush.ServerMapper;
import com.baomidou.springwind.service.IPushService.IPushService;
import com.baomidou.springwind.service.support.BaseServiceImpl;
import com.baomidou.springwind.utils.HttpClientUtils;
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.apache.commons.lang.StringUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;
import java.io.IOException;
import java.util.*;

/**
 * @Author: volc
 * @Description: 推送业务处理类
 * @Date: 16:19 2017/8/17
 */
@Service
public class PushServiceImpl extends BaseServiceImpl<PushMapper, Record> implements IPushService {

    private ObjectMapper objectMapper = new ObjectMapper();
    @Autowired
    private PushMapper pushMapper;
    @Autowired
    private ServerMapper serverMapper;

    @Autowired
    private RecordMapper recordMapper;

	// GOEASY_PUSH_URL=https://goeasy.io/goeasy/publish
	// String APPKEY = "appkey"; goeasy服务中注册应用时生成，客户端和推送端保持一致
    // String CHANNEL = "channel"; 推送端自行创建，客户端和推送端保持一致
    @Value("${GOEASY_PUSH_URL}")
    private String GOEASY_PUSH_URL;

    @Log("推送Web端")
    @Override
    public Map<String, Object> pushWeb(Map<String, Object> reqMap) throws IOException {
        Map<String, Object> resultMap = new HashMap<String, Object>();
        Map<String, String> pushParamMap = new HashMap<String, String>();
        HttpClientUtils httpClientUtils = new HttpClientUtils();
        // 根据sysId获取channel和appkey
        if (!StringUtils.isEmpty((String) reqMap.get("sysId"))) {
            Map<String, Object> serMap = new HashMap<String, Object>();
            serMap.put("status", "1");
            int sysId = Integer.parseInt((String) reqMap.get("sysId"));
            serMap.put("sysId", sysId);
            List<Server> serversList = serverMapper.selectByMap(serMap);
            if (null != serversList && serversList.size() > 0) {
                pushParamMap.put(ServerParam.APPKEY, serversList.get(0).getAppKey());
                pushParamMap.put(ServerParam.CHANNEL, serversList.get(0).getChannel());
            } else {
                resultMap.put("info", "该客户端没有注册");
            }
        }

        // 保存推送记录
        Record record = new Record();
        if (!StringUtils.isEmpty((String) reqMap.get("sysId"))) {
            int sysId = Integer.parseInt((String) reqMap.get("sysId"));
            record.setSysId(sysId);
        }
        record.setContent((String) reqMap.get("content"));
        Date date = new Date();
        record.setCreateTime(date);
        pushMapper.insertReId(record);
        PushContent pushContent = new PushContent();
        pushContent.setContent((String) reqMap.get("content"));
        pushContent.setMsgId(record.getId());
        pushContent.setUserId((String) reqMap.get("userId")); // 接收人ID
        pushContent.setType((String) reqMap.get("type")); // 消息类型，0 公告， 1 指定用户
        String content = objectMapper.writeValueAsString(pushContent);
        pushParamMap.put("content", content);
        String resultJson = httpClientUtils.postUrlencoded(GOEASY_PUSH_URL, pushParamMap);
        if (!StringUtils.isEmpty(resultJson)) {
            JsonNode jsonNode = objectMapper.readTree(resultJson);
            String code = jsonNode.get("code").asText();
            if (!code.equals("200")) {
                resultMap.put("info", "网络原因，推送失败");
                resultMap.put("success", "1");
            } else {
                resultMap.put("info", "推送成功");
                resultMap.put("success", "0");
            }
        } else {
            resultMap.put("info", "网络原因，推送失败");
            resultMap.put("success", "1");
        }
        return resultMap;
    }

    /**
     * @Author: volc
     * @Description: 客户端反馈消息
     * @Date: 18:27 2017/8/18
     */
    @Override
    public void pushMsg(int id) {
        Record record = new Record();
        record.setId(id);
        record.setStatus("0");
        pushMapper.updateById(record);
    }
}
```
### 1.3 客户端前台引入js
getMessage.js
```javascript
var goEasy = new GoEasy({appkey: Common key});
goEasy.subscribe({
    channel: 'channel', // 服务端生成，需保持一致，客户端才能接收到服务端推送的消息
    onMessage: function (message) {
        alert('接收到消息:' + message.content);
    }
});
```