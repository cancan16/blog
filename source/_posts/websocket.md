---
title: websocket
date: 2018-01-01 15:39:17
update: 2018-01-01 15:39:17
categories: WEBSOCKET
tags: [websocket]
---

### 1. spring整合websocket通信

#### 1.1 引入依赖
<!-- more -->
```
<!-- 使用spring websocket依赖的jar包 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-websocket</artifactId>
            <version>4.3.3.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-messaging</artifactId>
            <version>4.3.3.RELEASE</version>
        </dependency>
        <!-- 使用spring websocket依赖的jar包 -->

        <!-- websocket兼容ie9测试 -->
        <!-- https://mvnrepository.com/artifact/javax.websocket/javax.websocket-api -->
        <dependency>
            <groupId>javax.websocket</groupId>
            <artifactId>javax.websocket-api</artifactId>
            <version>1.1</version>
            <scope>provided</scope>
        </dependency>
        <!-- websocket兼容ie9测试 -->
```
项目启动加载websocket初始化 SpringWebSocketConfig
```
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;
import org.springframework.web.socket.config.annotation.EnableWebSocket;
import org.springframework.web.socket.config.annotation.WebSocketConfigurer;
import org.springframework.web.socket.config.annotation.WebSocketHandlerRegistry;
import org.springframework.web.socket.handler.TextWebSocketHandler;

@Configuration
@EnableWebMvc
@EnableWebSocket
public class SpringWebSocketConfig extends WebMvcConfigurerAdapter implements WebSocketConfigurer {

    /**
     * 项目启动初始化websocket，可以添加拦截器
     * @param registry
     */
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
        // 用来注册websocket server实现类，第二个参数是访问websocket的地址
        registry.addHandler(webSocketHandler(), "/webSocket").addInterceptors(new SpringWebSocketHandlerInterceptor()).setAllowedOrigins("*");
        // 这个是使用Sockjs的注册方法
        registry.addHandler(webSocketHandler(), "/webSocket/socketJs").addInterceptors(new SpringWebSocketHandlerInterceptor()).withSockJS();
    }

    @Bean
    public TextWebSocketHandler webSocketHandler() {
        return new SpringWebSocketHandler();
    }
}

```
SpringWebSocketHandlerInterceptorwebsocket拦截器

```
import org.springframework.http.server.ServerHttpRequest;
import org.springframework.http.server.ServerHttpResponse;
import org.springframework.http.server.ServletServerHttpRequest;
import org.springframework.web.socket.WebSocketHandler;
import org.springframework.web.socket.server.support.HttpSessionHandshakeInterceptor;

import javax.servlet.http.HttpSession;
import java.util.Map;

/**
 * WebSocket拦截器
 *
 * @author WANG
 */
public class SpringWebSocketHandlerInterceptor extends HttpSessionHandshakeInterceptor {


    // 注入httpRequest用于获取cookies;
    @Override
    public boolean beforeHandshake(ServerHttpRequest request, ServerHttpResponse response, WebSocketHandler wsHandler,
                                   Map<String, Object> attributes) throws Exception {
        System.out.println("Before Handshake");
		
		// (请求连接websocket时传递userid)
		HttpServletRequest servletRequest = ((ServletServerHttpRequest) request).getServletRequest();
		String eid = (String) servletRequest.getParameter("userid");
        // 从session中获取用户信息
        ServletServerHttpRequest servletRequest = (ServletServerHttpRequest) request;
        HttpSession session = servletRequest.getServletRequest().getSession(false);
        // 在这里设置用户信息，用于和连接时创建的websocket对象进行关联，一个websession对应一个用户
        attributes.put("user", "userid");
        System.out.println(session);
        // 建立连接时websocketsession中包含该用户的userId用于定向发送
        String userId = null;
        if (request instanceof ServletServerHttpRequest) {
        }
        return super.beforeHandshake(request, response, wsHandler, attributes);
    }

    @Override
    public void afterHandshake(ServerHttpRequest request, ServerHttpResponse response, WebSocketHandler wsHandler,
                               Exception ex) {
        // TODO Auto-generated method stub
        super.afterHandshake(request, response, wsHandler, ex);
    }

}
```
处理发送和接收消息核心类 SpringWebSocketHandler
```
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.socket.CloseStatus;
import org.springframework.web.socket.TextMessage;
import org.springframework.web.socket.WebSocketSession;
import org.springframework.web.socket.handler.TextWebSocketHandler;

import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
import java.util.Set;


/**
 * @Author: volc
 * @Description: 处理发送和接收消息核心类
 * @Date: 16:19 2017/9/22
 */
public class SpringWebSocketHandler extends TextWebSocketHandler {
    private static final Map<String, WebSocketSession> usersMap;//这个会出现性能问题，最好用Map来存储，key用userid
    private static Logger logger = LoggerFactory.getLogger(SpringWebSocketHandler.class);

    static {
        usersMap = new HashMap<String, WebSocketSession>();
    }

    public SpringWebSocketHandler() {
    }

    /**
     * @Author: volc
     * @Description: 连接成功时候，会触发页面上onopen方法
     * @Date: 16:19 2017/9/22
     */
    public void afterConnectionEstablished(WebSocketSession session) throws Exception {

        System.out.println("connect to the websocket success......当前数量:" + usersMap.size() + " 并触发页面的onOpen方法");
        //这块会实现自己业务，比如，当用户登录后，会把离线消息推送给用户
        //TextMessage returnMessage = new TextMessage("你将收到的离线");
        //session.sendMessage(returnMessage);
    }

    /**
     * @Author: volc
     * @Description: 关闭连接时销毁用户(客户端)和服务的websocket连接
     * @Date: 16:27 2017/9/22
     */
    public void afterConnectionClosed(WebSocketSession session, CloseStatus closeStatus) throws Exception {
        logger.debug("websocket connection closed......");
        String userId = (String) session.getAttributes().get("WEBSOCKET_USERID");
        System.out.println("用户" + userId + "已退出！");
        usersMap.remove(userId);
        System.out.println("剩余在线用户" + usersMap.size());
    }

    /**
     * @Author: volc
     * @Description: 获取客户端发送到服务端的信息
     * @Date: 16:28 2017/9/22
     */
    @Override
    protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
        String userId = message.getPayload();
        // 保存用户和websokcetsession对象关系,用用户
        session.getAttributes().put("WEBSOCKET_USERID", userId);
        usersMap.put(userId, session);
        super.handleTextMessage(session, message);
    }

    public void handleTransportError(WebSocketSession session, Throwable exception) throws Exception {
        if (session.isOpen()) {
            session.close();
        }
        logger.debug("websocket connection closed......");
        usersMap.remove((String) session.getAttributes().get("WEBSOCKET_USERID"));
    }

    public boolean supportsPartialMessages() {
        return false;
    }

    /**
     * @Author: volc
     * @Description: 给指定的用户发送消息
     * @Date: 16:27 2017/9/22
     */
    public void sendMessageToUser(String userName, TextMessage message) {
        Set<Map.Entry<String, WebSocketSession>> entries = usersMap.entrySet();
        for (Map.Entry<String, WebSocketSession> user : entries) {
            if (user.getKey().equals(userName)) {
                try {
                    if (user.getValue().isOpen()) {
                        user.getValue().sendMessage(message);
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
                break;
            }
        }
    }

    /**
     * @Author: volc
     * @Description: 给所有的用户发送消息
     * @Date: 16:26 2017/9/22
     */
    public void sendMessageToUsers(TextMessage message) {
        Set<Map.Entry<String, WebSocketSession>> entries = usersMap.entrySet();
        for (Map.Entry<String, WebSocketSession> user : entries) {
            try {
                if (user.getValue().isOpen()) {
                    user.getValue().sendMessage(message);
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

}

```
页面
```
<!--<script type="text/javascript" src="http://cdn.bootcss.com/sockjs-client/1.1.1/sockjs.js"></script>-->
<script src="#springUrl('/static/pushstatic/js/socketjs/sockjs.js')"></script>
<section>
    <div class="row">
        <article class="col-xs-12 col-sm-12 col-md-12 col-lg-12">
            <div class="jarviswidget jarviswidget-color-blueDark" id="wid-id-0" data-widget-editbutton="false">
                <header>
                    <span class="widget-icon"> <i class="fa fa-table"></i> </span>
                    <h2>webscoket推送测试</h2>
                </header>
                <div>
                    <div class="widget-body">
                        <div class="row">
                            <div class="col-xs-12" style="margin-bottom:10px;">
                                <div class="form-inline">
                                    <div class="input-group">
                                        <input class="form-control" placeholder="推送内容" id="contentId"/>
                                        <div class="input-group-btn">
                                            <button onclick="doSend();" class="btn btn-primary" type="button">
                                                <i class="fa fa-search"></i> 测试
                                            </button>

                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-xs-12" style="margin-bottom:10px;">
                                <div class="form-inline">
                                    <div class="input-group">
                                        <div>F12查看控制台，经过测试ie9及以下不兼容。原理以实现，功能尚未开发</div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </article>
    </div>
</section>
<script type="text/javascript">
    $(document).ready(function () {
    });
    var websocket = null;
    if ('WebSocket' in window) {
        websocket = new WebSocket("ws://localhost:8081/pushmanage/webSocket?userid=1236783");
    }
    else if ('MozWebSocket' in window) {
        websocket = new MozWebSocket("ws://localhost:8081/pushmanage/websocket/socketServer");
    }
    else {
        websocket = new SockJS("http://localhost:8081/pushmanage/sockjs/socketServer");
    }
    websocket.onopen = onOpen;
    websocket.onmessage = onMessage;
    websocket.onerror = onError;
    websocket.onclose = onClose;

    /**
     * @Author: volc
     * @Description: 连接成功后会触发onOpen方法并，关联用户与session之间的连接
     * @Date: 10:26 2017/9/25
     */
    function onOpen(openEvt) {
        var userId = "${userId}";
        websocket.send(userId); //调用后台handleTextMessage方法
    }

    function onMessage(evt) {
        console.log(evt.data);
    }

    function onError() {
    }

    function onClose() {
    }

    function doSend() {
        if (websocket.readyState == websocket.OPEN) { // 连接成功发送发送消息
            // 使用controller发送消息
            testToMsg();
        } else {
            alert("连接失败!");
        }
    }

    window.close = function () {
        websocket.onclose();
    }

    // 测试通过请求controller发送消息到该页面
    function testToMsg() {
        var reqData = {userId: "${userId}"};
        reqData.msg = $("#contentId").val();
        $.ajax({
            type: 'POST',
            url: "ws/websocket/send",
            contentType: "application/json; charset=utf-8",
            data: JSON.stringify(reqData),
            success: function (data) {
                console.log(data);
            }
        })
    }
</script>
```


### 管理websocket连接

```java
import com.baomidou.springwind.service.impl.push.HandleFromWebsocketMsg;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.web.socket.CloseStatus;
import org.springframework.web.socket.TextMessage;
import org.springframework.web.socket.WebSocketSession;
import org.springframework.web.socket.handler.TextWebSocketHandler;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
import java.util.Set;


/**
 * @Author: volc
 * @Description: 处理发送和接收消息核心类
 * @Date: 16:19 2017/9/22
 */
@Service
public class SpringWebSocketHandler extends TextWebSocketHandler {

    @Autowired
    private HandleFromWebsocketMsg handleFromWebsocketMsg;

    // 这个会出现性能问题，最好用Map来存储，key用userid(客户端类型_用户id 例 ANDROID_019957)
    private static final Map<String, WebSocketSession> usersMap;
    private static Logger logger = LoggerFactory.getLogger(SpringWebSocketHandler.class);

    static {
        usersMap = new HashMap<String, WebSocketSession>();
    }

    public Map<String, WebSocketSession> SpringWebSocketHandler() {
        return usersMap;
    }

    /**
     * @Author: volc
     * @Description: 连接后出发该方法，并触发页面上onopen方法
     * @Date: 16:19 2017/9/22
     */
    @Override
    public void afterConnectionEstablished(WebSocketSession session) throws Exception {
        String userId = (String) session.getAttributes().get("userId");
        usersMap.put(userId, session);
        logger.debug("=>=>=>=>=>=>=>=>=>=>=>=>=>=>=>=>用户id:" + userId + " websocket通信成功并处出发页面onOpen方法");
        logger.debug("=>=>=>=>=>=>=>=>=>=>=>=>=>=>=>=>当前在线人数:" + usersMap.size());
        //这块会实现自己业务，比如，当用户登录后，会把离线消息推送给用户
        //TextMessage returnMessage = new TextMessage("你将收到的离线");
        //session.sendMessage(returnMessage);
    }

    /**
     * @Author: volc
     * @Description: 关闭连接时销毁用户(客户端)和服务的websocket连接
     * @Date: 16:27 2017/9/22
     */
    @Override
    public void afterConnectionClosed(WebSocketSession session, CloseStatus closeStatus) throws Exception {
        String userId = (String) session.getAttributes().get("userId");
        logger.debug("=>=>=>=>=>=>=>=>=>=>=>=>=>=>=>=>用户id:" + userId + "离线");
        usersMap.remove(userId);
        logger.debug("=>=>=>=>=>=>=>=>=>=>=>=>=>=>=>=>当前在线人数:" + usersMap.size());
    }

    /**
     * @Author: volc
     * @Description: 获取客户端发送到服务端的信息
     * @Date: 16:28 2017/9/22
     */
    @Override
    protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
        String userId = message.getPayload();
        // 保存用户和websokcetsession对象关系,用用户
        session.getAttributes().put("WEBSOCKET_USERID", userId);
        usersMap.put(userId, session);
        // 通过请求数据type类型做响应的处理
        handleFromWebsocketMsg.handleMsg(message);
        super.handleTextMessage(session, message);
    }

    @Override
    public void handleTransportError(WebSocketSession session, Throwable exception) throws Exception {
        if (session.isOpen()) {
            session.close();
        }
        logger.debug("websocket connection closed......");
        usersMap.remove(session.getAttributes().get("WEBSOCKET_USERID"));
    }

    @Override
    public boolean supportsPartialMessages() {
        return false;
    }

    /**
     * @Author: volc
     * @Description: 给指定的用户发送消息
     * @Date: 16:27 2017/9/22
     */
    public boolean sendMessageToUser(String userId, TextMessage message) {
        Set<Map.Entry<String, WebSocketSession>> entries = usersMap.entrySet();
        boolean isOver = false; // 接收人在线并发送出去
        for (Map.Entry<String, WebSocketSession> user : entries) {
            if (user.getKey().equals(userId)) {
                try {
                    if (user.getValue().isOpen()) { // 用户在线直接发送消息
                        user.getValue().sendMessage(message);
                        isOver = true;
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
                break;
            }
        }
        return isOver;
    }

    /**
     * @Author: volc
     * @Description: 给所有的用户发送消息
     * @Date: 16:26 2017/9/22
     */
    public void sendMessageToUsers(TextMessage message) {
        Set<Map.Entry<String, WebSocketSession>> entries = usersMap.entrySet();
        for (Map.Entry<String, WebSocketSession> user : entries) {
            try {
                if (user.getValue().isOpen()) {
                    user.getValue().sendMessage(message);
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * @Author: volc
     * @Description: 发送车辆实时消息到web端
     * @Date: 11:35 2018/1/30
     */
    public boolean sendPatrolInfoToWeb(TextMessage message) {
        boolean isSend = false;
        Set<Map.Entry<String, WebSocketSession>> entries = usersMap.entrySet();
        try {
            for (Map.Entry<String, WebSocketSession> user : entries) {
                if (user.getKey().contains("WEB")) {
                    if (user.getValue().isOpen()) {
                        user.getValue().sendMessage(message);
                    }
                }
            }
            isSend = true;
        } catch (IOException e) {
            e.printStackTrace();
        }
        return isSend;
    }
}

```

处理用户消息和管理连接用户类
```java
import com.baomidou.springwind.common.EhcacheHelper;
import com.baomidou.springwind.common.ResponseInfo;
import com.baomidou.springwind.common.enums.Plant;
import com.baomidou.springwind.entity.push.MessageInfo;
import com.baomidou.springwind.entity.push.PatrolInfo;
import com.baomidou.springwind.service.websocket.SpringWebSocketHandler;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.apache.commons.lang.StringUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.web.socket.TextMessage;
import org.springframework.web.socket.WebSocketSession;

import java.io.IOException;
import java.util.*;

/**
 * @Author: volc
 * @Description: 通过页面传递的参数type类型进行响应处理
 * @Date: 19:54 2018/1/24
 */
@Service
public class HandleFromWebsocketMsg {

    @Autowired
    public SpringWebSocketHandler springWebSocketHandler;

    private static ObjectMapper MAPPER = new ObjectMapper();

    /**
     * @return
     */
    public ResponseInfo handleMsg(TextMessage textMessage) throws IOException {
        ResponseInfo responseInfo = new ResponseInfo();
        // 保存心跳包用于实时更新车辆信息
        // {"type":"xuncha","userId":"","obj":{"carid":"1234123231","companyid":"当前巡查人员所属公司id","company":"巡查人员所属公司名称","users":[{"id":"231231","name":"胡卫东"},{"id":"231231","name":"胡卫东"}],"longitude":"12343.3232","lat":"180.4322","speed":"12","israng":true}}
        MessageInfo messageInfo = MAPPER.readValue(textMessage.getPayload(), MessageInfo.class);
        if (null != messageInfo) {
            if (Plant.MessageType.PATROL.desc().equals(messageInfo.getType())) { // 判断请求是巡查车辆心跳包
                if (null == messageInfo.getObj()) {
                    responseInfo.setInfo("参数校验错误");
                    responseInfo.setSuccess(false);
                    return responseInfo;
                }

                PatrolInfo patrolInfoFrom = MAPPER.readValue(MAPPER.writeValueAsString(messageInfo.getObj()), PatrolInfo.class);

                if (StringUtils.isEmpty(patrolInfoFrom.getCarId()) || StringUtils.isEmpty(patrolInfoFrom.getCompany()) || StringUtils.isEmpty(patrolInfoFrom.getCompanyId()) || StringUtils.isEmpty(patrolInfoFrom.getLat()) || StringUtils.isEmpty(patrolInfoFrom.getLongitude()) || StringUtils.isEmpty(patrolInfoFrom.getSpeed()) || null == patrolInfoFrom.getUsers() || patrolInfoFrom.getUsers().size() <= 0) {
                    responseInfo.setInfo("参数校验错误，发送失败");
                    responseInfo.setSuccess(false);
                    return responseInfo;
                }
                // 查询该用户是否在线
                Map<String, WebSocketSession> stringWebSocketSessionMap = springWebSocketHandler.SpringWebSocketHandler(); // 获取连接websocket用户
                Set<String> keySet = stringWebSocketSessionMap.keySet();
                boolean isExist = false;
                for (String key : keySet) {
                    if (key.equals(messageInfo.getUserId())) {
                        isExist = true;
                        break;
                    }
                }
                // 获取缓存车辆缓存信息
                Object carinfo = EhcacheHelper.get("carinfo", "WEBSOCKET_PROTAL");
                List<PatrolInfo> patrolInfos = null;
                if (isExist) {
                    if (null != carinfo) {
                        patrolInfos = (ArrayList<PatrolInfo>) carinfo;
                        Iterator iterator = patrolInfos.iterator();
                        while (iterator.hasNext()) {
                            PatrolInfo patrolInfo = (PatrolInfo) iterator.next();
                            if (patrolInfo.getCarId().equals(patrolInfoFrom.getCarId())) {
                                iterator.remove();
                            }
                        }
                        patrolInfos.add(patrolInfoFrom);
                    } else {
                        patrolInfos = new ArrayList<PatrolInfo>();
                        String s = MAPPER.writeValueAsString(messageInfo.getObj());
                        PatrolInfo patrolInfo = MAPPER.readValue(s, PatrolInfo.class);
                        patrolInfos.add(patrolInfo);
                    }
                    EhcacheHelper.put("carinfo", "WEBSOCKET_PROTAL", patrolInfos);

                    boolean b = springWebSocketHandler.sendPatrolInfoToWeb(new TextMessage(MAPPER.writeValueAsBytes(patrolInfos)));
                    responseInfo.setInfo(b ? "发送成功" : "发送失败");
                    responseInfo.setSuccess(b);
                    return responseInfo;
                } else { // 用户不在线，请求该用户的缓存信息
                    if (null != carinfo) {
                        patrolInfos = (ArrayList<PatrolInfo>) carinfo;
                        Iterator iterator = patrolInfos.iterator();
                        while (iterator.hasNext()) {
                            PatrolInfo patrolInfo = (PatrolInfo) iterator.next();
                            if (patrolInfo.getCarId().equals(patrolInfoFrom.getCarId())) {
                                iterator.remove();
                            }
                        }
                        EhcacheHelper.put("carinfo", "WEBSOCKET_PROTAL", patrolInfos);
                    }
                    responseInfo.setInfo("用户已离线请重新连接，发送失败");
                    responseInfo.setSuccess(false);
                    springWebSocketHandler.sendPatrolInfoToWeb(new TextMessage(MAPPER.writeValueAsBytes(patrolInfos)));
                    return responseInfo;
                }
            } else if (Plant.MessageType.GETPATROL.desc().equals(messageInfo.getType())) { // web端获取车辆在线状态信息
                Object carinfo = EhcacheHelper.get("carinfo", "WEBSOCKET_PROTAL");
                responseInfo.setMsg(carinfo);
                responseInfo.setSuccess(true);
                responseInfo.setInfo("获取心跳包成功");
                return responseInfo;
            }
        }
        responseInfo.setInfo("未知类型请求");
        return responseInfo;
    }

}

```
