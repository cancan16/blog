---
title: js中逻辑或用法
date: 2017-03-29 23:30:33
update: 2017-03-29 23:30:33
categories: JS
tags: [js]
---
### 1 js中||的用法
<!-- more -->
js中||运算符的含义：
例如a || b
系统先判断A表达式的布尔值，是真是假。如果为真，直接返回a对象。如果为假，直接返回b对象(并不只是b的值，还有类型)。 
所以，var a = a || {}这行代码中如果a为false则直接返回null对象{}赋值给a。
这样写，虽然看着比较复杂点，但是效率高，特别是判断一些变量，属性或者是传参是否存在，如果存在就直接用，不存在，就赋予一个空变量。
```JavaScript
<script>
    window.onload = function () {
        test(1);
    }
    function test(a) { // 如果a为1，a的类型b值为number，如果a为0，a的类型b值为object
        a = a || {}; // 如果a为
        var b = typeof a;
        alert(b);
        if (a === '1') {
            alert(a);
        }
    }
</script>
```