---
title: HTML
date: 2016-10-18 22:03:22
update: 2016-10-18 22:03:22
categories: WORK
tags: [html, ueditor]
---
### input中autocomplete
```html
<!-- autocomplete：为on时返回页面时，input中的值仍然存在 -->
<input name="user_phone" id="user_phone" type="text" class="form-control required"   autocomplete="off" placeholder="请填写您注册的手机号码(登录账号)" required />
```
<!-- more -->
### UEditor使用注意事项
#### 引入UEditor和回显图片
* jsp页面引用：

```html
<!-- 文档编辑器 -->
<script type="text/javascript" charset="utf-8"
	src="${contextPath}/resource/scripts/ueditor/ueditor.config.js"></script>
<script type="text/javascript" charset="utf-8"
	src="${contextPath}/resource/scripts/ueditor/ueditor.all.min.js">

</script>
<script type="text/javascript" charset="utf-8"
	src="${contextPath}/resource/scripts/ueditor/lang/zh-cn/zh-cn.js"></script>
<script>
	window.UEDITOR_HOME_URL = "${contextPath}/resource/scripts/ueditor/";
	var ue = UE.getEditor('editor');
</script>
<label><h5>&nbsp;&nbsp;静态页面内容：</h5></label>
<textarea id="editor" name="fContent"
	style="width: 1500px; height: 400px" data-options="required:true"></textarea>
</div>
```

-----
#### 设置获取ueditor内容
```javascript
//判断ueditor 编辑器是否创建成功并设置文本内容
ue.addListener("ready", function () {
ue.setContent(staticPage.fContent);
ue.setContent('hello');
var html = ue.getContent();
var txt = ue.getContentTxt();
  alert(txt);
});
```

### 输入框对齐
> 方式一：

```html
<dl style="width:253px;float:left">
	<dt style="width:50px;float:left">站点名称</dt>
	<dd style="width:200px;float:left">
		<input type="text" name="stationName" id="stationName" />
	</dd>
	<dt style="width:50px;float:left">手机号</dt>
	<dd style="width:200px;float:left">
		<input type="text" name="tel" id="tel" />
	</dd>
</dl>
```
> 方式二：

```html
<ul>
	<li>
		<span id='spanId' style="width:200px;float:left;">
			标签121231：
		</span>
		<input type="text"></input>
	</li>
	<li>
		<span id='spanId2' style="width:200px;float:left;">
			标签：
		</span>
		<input type="text"></input>
	</li>
</ul>
```
> 方式三：

放入加入style样式table中

### 失去焦点事件
```html
onblur=”validate(this, 0)”
validate: function(item, index) {
}

```

### html传值遇到的问题

```
// 获取页面之间跳转get方式传过来的值
function getQueryStr(str) {
	var rs = new RegExp("(^|)" + str + "=([^&]*)(&|$)", "gi").exec(String(window.document.location.href)), tmp;
	if (tmp = rs) {
		return decodeURI(tmp[2]);
	}
	return "";
}
```
遇到的问题是有些浏览器编码不同导致传入到另一个html页面时中文参数出现乱码，解决方式是："&name=" + encodeURI("中文参数")
在html页面get请求服务器接口时也会出现乱码，解决方式是："&name=" + encodeURI(encodeURI("中文参数"))，进行两次encode编码。

### html映射到后台复杂集合对象

```java
import lombok.Data;
import javax.persistence.Transient;
import java.util.List;

@Data
public class BannerExpand extends Banner {
    private List<Integer> sorts;
    private List<List<String>> mallCodes;
}

```

```html
<div>
<form>
	<div>
	    <div>
	    	<!-- 只能选择一个option 映射到BannerExpand中sorts第一个元素 -->
            <select name="sorts" class="form-control">
            </select>
	    </div>
	    <div>
	    	<!-- 可选择多个option 映射到BannerExpand中mallCodes第一个元素(List<String>) -->
            <select name="mallCodes[0]" multiple="multiple"
                    class="add-banner-mall">
                <optgroup th:each="cityMall : ${cityContaiMall}"
                          th:label="${cityMall.city}"
                          class="group-1">
                    <option th:each="mall : ${cityMall.malls}"
                            th:value="${mall.mallCode}"
                            th:text="${mall.mallName}">
                    </option>
                </optgroup>
            </select>
	    </div>
	</div>
	<div>
	    <div>
	    	<!-- 只能选择一个option 映射到BannerExpand中第二个元素 -->
            <select name="sorts" class="form-control">
            </select>
	    </div>
	    <div>
	    	<!-- 可选择多个option 映射到BannerExpand中mallCodes第二个元素(mallCodes[1]) -->
            <select name="mallCodes[1]" multiple="multiple"
                    class="add-banner-mall">
                <optgroup th:each="cityMall : ${cityContaiMall}"
                          th:label="${cityMall.city}"
                          class="group-1">
                    <option th:each="mall : ${cityMall.malls}"
                            th:value="${mall.mallCode}"
                            th:text="${mall.mallName}">
                    </option>
                </optgroup>
            </select>
	    </div>
	</div>
<form>
</div>
```
