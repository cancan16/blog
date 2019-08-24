---
title: JS
date: 2016-11-01 22:05:27
update: 2016-12-22 10:42:27
categories: JS
tags: [js,baidumap,echarts,ztree]
---

### 1 windows对象setTimeOut()用于发送验证码倒计时

```
//短信验证码倒计时
var wait = 30;
function time(o) {
    if (wait == 0) {
        o.removeAttribute("disabled");
        o.innerHTML = "发送短信验证码";
        wait = 30;
    } else {
        o.setAttribute("disabled", true);  
        o.innerHTML = "重新发送(" + wait + ")";

        wait--;  
        setTimeout(function(){time(o)}, 2)
    }  
}
```
<!-- more -->
### 2 bind(this)用法
```
//设立一个简单地对象作为“上下文”
 var context = { foo: "bar" };

 //一个在this上下文中指向foo变量的函数
 function returnFoo () {
   return this.foo;
 }

 // 变量在作用域中不存在，因此显示undefined
 returnFoo(); // => undefined

 // 如果我们把它绑定在context上下文中
 var bound = returnFoo.bind(context);

 // 现在的作用域中有这个变量了
 bound(); // => "bar"


```
### 3 js传数组给html
JS
```JS
var servicePlanList = JSON.stringify(canFee);  // canFee数组
this.assign('servicePlanList', servicePlanList);

```

html

```html
<INPUT ID="servicePlanList" NAME="servicePlanListName" TYPE="hidden"
  VALUE="<?=servicePlanList?>"></INPUT> value的值” [
{"fState":"1","managerId":123,"serviceFeeId":"5b7627c5ec8d4b96abae3996255f1034","serviceFeeMin":34,"serviceFeeName":"KGKGKA134","serviceFeePer":"12","serviceFeeStart":23,"type":1},
{"fState":"1","managerId":123,"serviceFeeId":"5a1248caf9334d77ab04f6a1df68b542","serviceFeeMin":20,"serviceFeeName":"KGKGKA13","serviceFeePer":"12","serviceFeeStart":23,"type":1}]
”
<SCRIPT>
var servicePlanList = $('#servicePlanList').val();
servicePlanList = eval("(" + servicePlanList +")"); // eval()语言
</SCRIPT>
```
### 4 解决服务器上容器传参到jsp页面出现乱码问题
1方式.
前台：userName : encodeURI(encodeURI($(user.children[1]).text())),
后台：userInfoJson = URLDecoder.decode(userInfoJson, "UTF-8");
2方式.
后台：keyWords = new String(keyWords.getBytes("ISO-8859-1"), "UTF-8");

### 5 js 全局变量赋值问题

```
注意：全局变量一定要放在使用全局变量方法的上面，
function setI(){i=0;}
var i = 7;
function getI(){console.log(i);} // 打印仍为7 
```
```javascript
$(function(){ // 全局变量的引用，另一个页面也可以	
	// 获取全局变量
})
```
### 6 时间戳转换成指定格式字符串
```
// 	2008-07-31 23:01:59
function formatDate(dateStr) { // long型的   1217516519000
	var date = new Date(dateStr);
	Y = date.getFullYear() + '-';
	M = (date.getMonth() + 1 < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1) + '-';
	D = (date.getDate() < 10 ? '0' + (date.getDate()) : date.getDate()) + ' ';
	h = (date.getHours() < 10 ? '0' + (date.getHours()) : date.getHours()) + ':';
	m = (date.getMinutes() < 10 ? '0' + (date.getMinutes()) : date.getMinutes()) + ':';
	s = (date.getSeconds() < 10 ? '0' + (date.getSeconds()) : date.getSeconds());
	return Y + M + D + h + m + s;
}

var stringTime = "2014-07-10 10:21:12";
var timestamp2 = Date.parse(new Date(stringTime)); // long型时间戳
console.log('时间');
console.log(formatDate(timestamp2));
```
### 7 根据id添加该标签的属性
```
// 根据id添加该标签的class属性
$("#deviceStatusId")[0].setAttribute('class', statusClass);
```

### 8 cookie操作
```
// 添加存到cookie中
document.cookie = 'positionValue=1';
// 读取cookie
console.log(getCookie('positionValue'));
// 设置cookie		
setCookie('positionValue', '2', 1); // 有效期1天数

function getCookie(c_name) {
	if (document.cookie.length > 0) {　　//先查询cookie是否为空，为空就return ""
		c_start = document.cookie.indexOf(c_name + "=")　　//通过String对象的indexOf()来检查这个cookie是否存在，不存在就为 -1　　
		if (c_start != -1) {
			c_start = c_start + c_name.length + 1　　//最后这个+1其实就是表示"="号啦，这样就获取到了cookie值的开始位置
			c_end = document.cookie.indexOf(";", c_start)　　//其实我刚看见indexOf()第二个参数的时候猛然有点晕，后来想起来表示指定的开始索引的位置...这句是为了得到值的结束位置。因为需要考虑是否是最后一项，所以通过";"号是否存在来判断
			if (c_end == -1) c_end = document.cookie.length
			return unescape(document.cookie.substring(c_start, c_end))　　//通过substring()得到了值。想了解unescape()得先知道escape()是做什么的，都是很重要的基础，想了解的可以搜索下，在文章结尾处也会进行讲解cookie编码细节
		}
	}
	return ""
}

function setCookie(c_name, value, expiredays) {
	var exdate = new Date();
	exdate.setDate(exdate.getDate() + expiredays);
	document.cookie = c_name + "=" + escape(value) + ((expiredays == null) ? "" : ";expires=" + exdate.toGMTString());
}
```

### 9 js触发a标签
```
<a id="liebiaoid" data-toggle="tab" style="cursor:pointer"><span id="liebiaosid">列 表</span></a>
点击事件中添加跳转路径
$('#liebiaoid').click(function(){
	$('#liebiaoid')[0].setAttribute('href', "#rgcs"); // 动态添加
	$('#liebiaosid').click();
});
```

### 10 jquery树插件

```
<link href="js/jquery-ztree/3.5.12/css/zTreeStyle/zTreeStyle.min.css" rel="stylesheet" type="text/css"/>
<script src="js/jquery-ztree/3.5.12/js/jquery.ztree.all-3.5.min.js"></script>

<div class="modal fade" id="luxian" tabindex="-1" role="dialog" aria-labelledby="ModalLabel">
    <div class="modal-dialog2" role="document">
        <div class="modal-content">
            <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span>
                </button>
                <h4 class="modal-title" id="ModalLabel">选择所在路线</h4>
            </div>
            <div class="modal-body">
                <div class="row">
                    <div class="col-md-12">
                        <div id="treegs" class="ztree ztreeg"></div>
                    </div>
                </div>
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-primary">确定</button>
                <button type="button" class="btn btn-default" data-dismiss="modal">关闭</button>
            </div>
        </div>
    </div>
</div>
```

```
$(function(){
 var setting = {
	data: {
		key: {
			name: "name"
		},
		simpleData: {
			enable: true,
			idKey: "id",
			pIdKey: "pId",
			rootPId: '0'
		},
		view: {
			selectedMulti: false
		}
	},
	callback: {
		onClick: function (event, treeId, treeNode) {
			var name = treeNode.name;
			$("#lux").val(name); // 点击赋值
			$("#luxian").modal("hide"); // 点击后隐藏模态框
		}
	}
};

$.getJSON("/dhms/sys/device/deviceInfo/getAllLux", function (data) { // 获取树结构
	$.fn.zTree.init($("#treegs"), setting, data).expandAll(true);
});
})
```
### 11 引入百度地图

```
<script type="text/javascript" src="http://api.map.baidu.com/api?v=2.0&amp;ak=ohVowrtf6KyzxGHYWP6DLwDO"></script>
<script type="text/javascript" src="http://api.map.baidu.com/getscript?v=2.0&amp;ak=ohVowrtf6KyzxGHYWP6DLwDO&amp;services=&amp;t=20170801112042"></script>
<div id="allmap" style="overflow: hidden;border:1px solid #ccc;">
</div>


$(function(){
	//初始化地图高度
	var h = document.documentElement.clientHeight;//可见区域高度
	var divh = document.getElementById('allmap');
	divh.style.height = (h - 310) + "px";
	
	//加载百度地图

	// 创建Map实例
	map = new BMap.Map("allmap");
	// 初始化地图,设置中心点坐标和地图级别
	map.centerAndZoom(new BMap.Point(118.77589, 32.061958), 11);
	//添加平移缩放控件
	map.addControl(new BMap.NavigationControl());
	//添加放大、缩小控件
	map.addControl(new BMap.ScaleControl());
	//允许鼠标滑轮操作
	map.enableScrollWheelZoom();
})
```

### 12 echarts图标

```
<script src="js/echarts.js"></script>
<style>
    .charClass {
        height: 45px;
        width: 100%;
        margin: 0 auto;
    }
</style>

<div id="shid" class='charClass'></div>

$(function(){
	var myChartshid = echarts.init(document.getElementById('shid'));
	 optionshid = {
		tooltip : {
			trigger: 'axis', // 鼠标悬停触发
			show:true
		},
		grid: {
			left: '2%',
			right: '2%',
			bottom: 25, // 可调整图标在div中的位置
			containLabel: true
		},
		xAxis: {
			show:false,
			data : ['1','2','3','4','5','6','7','8','9','10','11','12','13','14','15','16','17','18','19','20','21','22','23','24','25','26','27','28','29','30','31','32','33','34','35','36','37','38','39','40','41','42','43','44','45','46','47','48','49','50'] // 50个点
		},
		yAxis: {
			show: false
		},
		series: [{
			type: 'line',
			data: ['1','2','3','4','5','6','7','8','9','10','11','12','13','14','15','16','17','18','19','20','21','22','23','24','25','26','27','28','29','30','31','32','33','34','35','36','37','38','39','40','41','42','43','44','45','46','47','48','49','50'] // 50个点的y值，个数要和xAxis的data数量一致
		}]
    };
	myChartshid.setOption(optionshid);
})
```

### 13. 使用jquery-form.js解决兼容性表单上传文件

```
<script type="text/javascript" src="js/jquery-form.js"></script>

<form id="importStationFormId" action="upload/upload" enctype="multipart/form-data" method="post"
	  class="smart-form">
	<div class="modal-body">
		<div class="widget-body no-padding" style="margin: 20px;">
			<fieldset>
				<section>
					<label class="label">选择文件</label>
					<label for="fileId" class="input input-file">
						<!--<div class="button">-->
						<input id="fileId" type="file" name="upfile">
						<!--浏览-->
						<!--</div>-->
						<!--<input type="text" placeholder="请选择文件" readonly="">-->
					</label>
				</section>
			</fieldset>
		</div>
	</div>
	<div class="modal-footer" style="padding: 20px;">
		<button type="button" id="closeId" class="btn btn-default btntop" data-dismiss="modal">关闭</button>
		<button type="button" onclick="importStationInfo()" class="btn btn-primary btntop">保存</button>
	</div>
</form>


 $("#importStationFormId").ajaxSubmit({
	dataType: 'json',
	success: function (data) {
		if (data.success == '0') {
			alert('导入成功');
		}
	}
});


后台：
@RequestMapping(value = "/upload")
@ResponseBody
public String upload(MultipartFile upfile, HttpServletRequest request, HttpServletResponse response)
		throws Exception {
	ReadExcelToList readExcelToList = new ReadExcelToList();
	List<StationBase> stationList = readExcelToList.getList(upfile);
	// 保存站点设备数据
	if (null != stationList && stationList.size() > 0) {
		Map<String, Object> paramMap = new HashMap<String, Object>();
		paramMap.put("stationDeviceList", stationList);
		Map<String, Object> resultMap = stationService.insertBaseStationInfo(paramMap);
		// 跳转页面
		return MAPPER.writeValueAsString(resultMap);
	}
	return null;
}
```

### select option操作
```
// 根据option的值选中
$("#denseselectid option[value='2']").attr("selected", "selected");

$("#deviceStatusId").get(0).selectedIndex = 0; // 获取到第一个select标签对象，并将第一个option选中
```
// select改变事件
```
$("#limitid").on("change", function () {
	console.log($("option:selected", this).val());
	if ($("option:selected", this).val() == '1') { // 永久就没有结束时间
		$('#endDate').attr("disabled", "disabled");
	} else {
		$('#endDate').removeAttrs("disabled");
	}
});
```

### 执行html初始化方法

```javascript
window.onload=function(){内容}
```

```javascript
$(function(){内容});
```

### radio操作

#### 根据value值选中radio

```html
<!DOCTYPE html>
<html>
<script type="text/javascript" src="../static/jquery-3.3.1.min.js"></script>
<body>

<h1>我的第一张网页</h1>
<input type="radio" name="gender" value="1">男
<input type="radio" name="gender" id="girlId" value="0">女

<p id="demo">我的第一个段落</p>

<script>

	$(function(){
		$("[name=gender][value='0']").prop("checked",true);
	})
</script>

</body>
</html>
```

#### boostrap下根据value选中radio

```html
<label class="col-md-3 control-label">性别</label>
<div class="col-md-9">
    <label class="radio-inline">
        <input type="radio" name="gender" id="boyId"
               value="1">男
    </label>
    <label class="radio-inline">
        <input type="radio" name="gender" id="girlId"
               value="0">女
    </label>
</div>


// 选中radio
$("[name=gender][value="+ data.gender+"]").prop("checked", "checked");
$("[name=gender][value="+ data.gender +"]").click();
```

### ztree插件的坑

数据中pid和pId大小写问题，
应该指定pid

```js
var setting = {
	check: {
		enable: true
	},
	data: {
		simpleData: {
			enable: true,
			// 指定数据中的父节点字段为pId
			pIdKey: "pId"
		},
		key: {
			name: "mallName"
		}
	}
};
```	
### 接口接受复杂的js数组问题

接口中不能直接接受复杂的js数组，如{[id:123,name:"123]}

只能接受简单的数组元素，如{"123","232"},

```js
let arr = ["123","1234"];
$.ajax({
    url: "",
    type: "POST",
    data: {"arr": arr},
    success: function (data) {
    }, error: function (data) {
    }
});
```    
后台：接参

```java
@RestController
@RequestMapping("/ww")
public class Test {
    @PostMapping(value = "/tt")
    public void tt(@RequestParam("arr[]") List<String> arr) {

    }
}
```
如果要接受复杂对象，需要转成json字符串，JSON.stringify(arr)
