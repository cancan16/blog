---
title: thymeleaf
date: 2018-10-07 14:44:02
update: 2018-10-07 14:44:02
categories: thymeleaf
tags: [thymeleaf]
---

### js动态添加select选项和取消select选项

<!-- more -->

test.html
```html
<!DOCTYPE html>
<html>
<meta charset="UTF-8">
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<script type="text/javascript" src="../static/jquery-3.3.1.min.js"></script>
<body>
<div>
	<div id="addbannermallid">
    </div>
    <script type="text/html" id="thymeleafTable">
        <div id="selectmallsid" class="add-banner-sort">
            <div class="form-group">
                <label class="col-sm-3 control-label">选择位置:</label>
                <button type="button"
                        onclick="deleteAddBannerMallCode(this)"
                        name="cancelselect"
                        class="btn cancel_select">取消
                </button>
                <div class="col-sm-4">
                    <select name="sorts" class="form-control">
                    </select>
                </div>
            </div>
            <div class="form-group">
                <label class="col-sm-3 control-label">选择商场:</label>
                <div class="col-sm-7 select_clazz">
                    <select name="mallCodes" multiple="multiple"
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
        </div>
    </script>
</div>
<script>
	$(function(){
		// 将thymeleafTable的html内容添加到addbannermallid标签中
		$("#addbannermallid").append($("#thymeleafTable").html());
	})
</script>
</body>
</html>
```

后台controller

```java
@RequestMapping("/test")
public ModelAndView test() {
    ModelAndView modelAndView = new ModelAndView();
    modelAndView.setViewName("test");
    List<MallCity> cityContaiMall = mallMapper.findCityContaiMall();
    modelAndView.addObject("cityContaiMall", cityContaiMall);
    return modelAndView;
}
```


