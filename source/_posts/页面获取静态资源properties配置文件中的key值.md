---
title: 页面获取静态资源properties配置文件中的key值
date: 2017-08-24 17:59:15
update: 2017-08-24 17:59:15
categories: HTML
tags: [html]
---

### 1. 页面获取配置文件中的key值
<!-- more -->

登录成功后，加载index.html页面，在index页面中引入获取资源的getProperties.js
getProperties.js
```javascript
var configParam = null;
$.ajax({
    type: 'POST',
    url: "readProperties/properties",
	// async: false, // 同步请求会出现以下警告
    data: JSON.stringify({}),
    success: function (data) {
        configParam = data;
    }
});
```
index.html
```html
<!-- S 获取resource中配置文件中的request.properties中的key值 -->
<script type="text/javascript" src="#springUrl('/static/pushstatic/js/definejs/getProperties.js')"></script>
<!-- E -->
<script>
    $(document).ready(function(){
		// console.log(configParam); 
		// 这样是获取不到configParam的值的。
		// index.html加载顺序:getProperties.js(ajax并没有请求)->ready方法中的console.log(configParam);-> getProperties.js中的请求
    });
</script>
```
警告：
![async](https://volc1612.gitee.io/blog/images/async/async.png)
出现的场景：
后台返回index.html视图，浏览器加载，index引入的getProperties.js,而在js中同步请求资源，才会出现警告。
这句警告意思是：
XMLHttpRequest  【ajax】发送请求，是在浏览器js的主线程上(加载index.html页面)，ajax同步发送请求引起的，这样会引起很不好的用户体验，可能导致浏览器卡死等等！
警告是针对HTML5说的，因为在html5以前，JavaScript是完全的单线程方式，主线程之外不存在其他线程。但在Html5中增加了Worker对象，每个Worker运行在一个独立的线程中，Worker线程被阻塞一般是不会影响主线程和浏览器的。因此，如果非要使用同步的Ajax（这种情况应该很少见），那就放到Worker线程中吧，千万千万不要放到主线程里。
解决方法：
1. 子页面中获取console.log(configParam)是可行的。
2. 加载index.html时后台返回model到页面中。

```java
import java.io.InputStream;
import java.util.Properties;

/**
 * @Author: volc
 * @Description: 获取配置文件工具类
 * @Date: 17:14 2017/8/23
 */
public class ConfigProperties {
    private Properties props;

    public Properties getProperties() {
        if (props == null) {
            props = new Properties();
            InputStream in;
            try {
                in = getClass().getResourceAsStream("/properties/request.properties");
                props.load(in);
            } catch (Exception e) {
            }
        }
        return props;
    }

    public static void main(String[] args) {
        ConfigProperties configProperties = new ConfigProperties();
        Properties properties = configProperties.getProperties();
        System.out.println(properties);
    }

}

```
