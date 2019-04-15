---
title: jsp页面之间传值
date: 2016-06-03 23:56:33
update: 2016-06-03 23:56:33
categories: JAVA
tags: [jsp]
---

### 1 jsp页面之间传值

<!-- more -->

index.jsp
```javacsript
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ page trimDirectiveWhitespaces="true"%>
<%@ page session="false"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<title></title>
<script type="text/javascript" src="/js/jquery-1.2.6.min.js"></script>
</head>
<body>
</body>
<script>
	$ (document).ready (function () {
		<%request.getRequestDispatcher("./success.jsp?a=1&b=2").forward(request, response);%>
	});
</script>
</html>

```
success.jsp
```javacsript
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ page trimDirectiveWhitespaces="true"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<script type="text/javascript" src="/js/jquery-1.2.6.min.js"></script>
</head>
<body id="mainframe">
	<div class="w" id="headers">
		<input id="pid" type="hidden" value="${param}" />
	</div>
</body>
<script>
	$ (document).ready (function () {
	    alert ($ ('#pid').val ()); // {b=2, a=1}
    });
</script>
</html>
```

### 2 jsp页面session存取数据

```
存：
<% request.getSession().setAttribute("userLoginName", getUser().getLoginName()); %>
取：
console.log('${sessionScope.userLoginName }');
```
