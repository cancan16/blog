---
title: layer
date: 2017-12-11 01:56:20
update: 2017-12-11 01:56:20
categories: LAYUI
tags: [layui]
---

### 1 等待加载样式，手动关闭

<!-- more -->
![desktop](https://volc1612.gitee.io/blog/images/layer/layer.png)
```
var id = layer.msg('处理中', {
	icon: 16,
	shade: 0.4,
	time: false
});
layer.close(id);//手动关闭
```

### 2 弹窗
```
layer.open({
  title: "使用问题解答",
  type: 2, 
  closeBtn: false,
  shadeClose: true,
  anim: 5,
  area: ['70%', '70%'],
  offset:['50px','200px'],
  content: ['/cloud/sys/cloud/getProblem', 'true']
});
```
### 3 前端分页
	
```
<div id="demo20"></div>

js
// 填充数据和分页
function getPage(data) {
	layui.use(['laypage', 'layer'], function () {
		var laypage = layui.laypage
			, layer = layui.layer;
		//调用分页
		laypage.render({
			elem: 'demo20'
			, count: data.length
			, jump: function (obj) {
				//模拟渲染
				document.getElementById('biuuu_city_list').innerHTML = function () {
					var arr = []
						, thisData = data.concat().splice(obj.curr * obj.limit - obj.limit, obj.limit);
					var recordListHtml = "";
					layui.each(thisData, function (index, item) {
						var status = ""; // 推送状态
						status = "推送成功"; // 客户端收到推送信息
						if (item.status == '1')
							status = "已推送"; // 已推送
						recordListHtml = recordListHtml;
					});
					$('#recordRowId').html(recordListHtml);
					return arr.join('');
				}();
			}
		});
	});
}
```

### 4 layer后端分页

```
<div id="pageOpId"></div>
// 查询数据列表
function getList(dataParam) {
	$.ajax({
		type: "POST",
		url: 'stationDevice/getListInfo',
		data: JSON.stringify(dataParam),
		contentType: "application/json; charset=utf-8",
		success: function (data) {
			if (data.success == '0') { // 查询成功赋值
				var html = "";
				if (data.resultList != null) {
				}
			}
			// 对数据进行分页处理
			initList(dataParam, data);
		}
	});
}

// 对数据进行分页处理
function initList(param, resultData) {
	layui.use(['laypage', 'layer'], function () {
		var laypage = layui.laypage;
		//完整功能
		laypage.render({
			elem: 'pageOpId',
			count: resultData.count, // 总条数
			curr: resultData.curr,
			layout: ['count', 'prev', 'page', 'next', 'skip'],
			jump: function (obj, first) { // (分页数据，是否第一次执行)
				// 查询数据并分页
				if (first) { // 第一次执行不做任何处理
				} else { // 第二次查询做处理
					param.curr = obj.curr;
					// 查询数据
					getList(param);
				}
			}
		});
	});
}
```