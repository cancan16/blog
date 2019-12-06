---
title: interview question
date: 2018-07-22 17:18:21
update: 2018-10-07 07:24:00
categories: interview
tags: interview
---

<div align="center">
<img style="text-align: center" src="https://volc1612.gitee.io/blog/images/interview-question/a.jpeg" alt="mianshi">
</div>
<!-- more -->

### 接口和抽象类的区别

> 什么是抽象方法

抽象方法就是以abstract修饰的方法，这种方法只声明返回的数据类型，方法名称和所需要的参数，没有方法体，也就是说抽象方法只需要声明而不需要事先，当一个方法为抽象方法时，意味着这个方法必须被子类的方法所重写，否则其子类的该方法仍然是abstract的，而这个子类也必须是抽象的，即声明为abstract

> 区别

接口是特殊的抽象类，自JDK8以来

* 成员变量 -> 接口中成员变量只能是`public static final`类型，抽象类中的成员变量可以是各种类型的。
* 方法 -> 接口中可以有`public abstract抽象方法`、`static方法`、`static代码块`、`default`方法，抽象类中可以有`public abstract抽象方法`、`static方法`、`static代码块`、`一般类型方法`，但是不能有`default`方法。
* 抽象类在可以在非抽象一般方法中提供成员方法的实现细节，而接口中不能存非抽象方法，如不支持`public void a(){}`。
* 一个类只能继承一个抽象类，而一个类只能实现一个或多个接口。

```java
public interface PersonIterface {
    /**
     * S jdk8之前版本
     * interface中只能存在public static final类型的成员变量，和public abstract类型的方法。
     * 不能存在static代码块
     */
    public static final int field = 0;

    // 等价于上一行代码
    int field1 = 0;
    public abstract void method(int a) throws Exception;
    void method1(int a) throws Exception;
    /**
     * E jdk8之前
     */
    /**
     * S jdk8以及以后的版本中
     * interface中可以定义public static final类型的成员变量，和public abstract类型的方法，新增了static和default方法。
     * 不能存在static代码块
     * 静态方法，只能通过接口名调用，不可以通过实现类的类名或者实现类的对象调用。default方法，只能通过接口实现类的对象来调用
     */
    // static修饰符定义静态方法
    static void staticMethod() {
        System.out.println("接口中的静态方法");
    }
    // default修饰符定义默认方法
    default void defaultMethod() {
        System.out.println("接口中的默认方法");
    }
    /**
     * E jdk8以及以后的版本中
     */
    int testa = 2;

    public static void main(String[] args) {
        
    }
}
```

```java
/**
 */
public abstract class PersonAbstract {

    public static final int field = 0;
    // 等价于上一行代码
    int field1 = 0;

    public abstract void method(int a) throws Exception;

    abstract void method1(int a) throws Exception;

    static void staticMethod() {
        System.out.println("接口中的静态方法");
    }

    static {
        int a = 1;
        System.out.println(a);
    }

    protected void test(){
    }

    public static void main(String[] args) {

    }
}
```

![interfaceandabstract_a](https://volc1612.gitee.io/blog/images/interview-question/interfaceandabstract_a.png)

> 接口的作用

- 重要性
在Java语言中， abstract class 和interface 是支持抽象类定义的两种机制。正是由于这两种机制的存在，才赋予了Java强大的 面向对象能力。
- 简单、规范性
如果一个项目比较庞大，那么就需要一个能理清所有业务的架构师来定义一些主要的接口，这些接口不仅告诉开发人员你需要实现那些业务，而且也将命名规范限制住了（防止一些开发人员随便命名导致别的程序员无法看明白）。
- 维护、拓展性
比如你要做一个画板程序，其中里面有一个面板类，主要负责绘画功能，然后你就这样定义了这个类。可是在不久将来，你突然发现这个类满足不了你了，然后你又要重新设计这个类，更糟糕是你可能要放弃这个类，那么其他地方可能有引用他，这样修改起来很麻烦。
如果你一开始定义一个接口，把绘制功能放在接口里，然后定义类时实现这个接口，然后你只要用这个接口去引用实现它的类就行了，以后要换的话只不过是引用另一个类而已，这样就达到维护、拓展的方便性。
- 安全、严密性
接口是实现软件松耦合的重要手段，它描叙了系统对外的所有服务，而不涉及任何具体的实现细节。这样就比较安全、严密一些（一般软件服务商考虑的比较多）。

> 抽象类的作用

- 为子类提供一个公共的类型；
- 封装子类中重复内容（成员变量和方法）；
- 定义有抽象方法，子类虽然有不同的实现，但该方法的定义是一致的。

> 接口和抽象类为什么不能实例化对象呢

因为接口和抽象类中都含有抽象方法，这些方法没有具体的实现细节，实例化的接口或抽象对象访问其中的方法没有任何意义。


### JVM加载class文件的原理机制

JVM中类的装载是由ClassLoader和它的子类来实现的,它负责在运行时查找和装入类文件的类。 
类装载器把一个类装入Java虚拟机中，要经过三个步骤来完成：装载、链接和初始化，其中链接又可以分成校验、准备、解析装载：查找和导入类或接口的二进制数据(类的编写格式)； 链接：执行下面的校验、准备和解析步骤，其中解析步骤是可以选择的； 校验：检查导入类或接口的二进制数据的正确性； 准备：给类的静态变量分配并初始化存储空间； 解析：将符号引用转成直接引用；初始化：激活类的静态变量,初始化Java代码和静态Java代码块

### jvm内存结构

![c](https://volc1612.gitee.io/blog/images/interview-question/c.png)

java内存主要分为6部分，分别是程序计数器，虚拟机栈，本地方法栈，堆，方法区和直接内存

1、程序计数器
线程私有，即每个线程都会有一个，线程之间互不影响，独立存储。
代表着当前线程所执行字节码的行号指示器。

2、虚拟机栈
线程私有，它的生命周期和线程相同。
描述的是java方法执行的内存模型：每个方法在执行的同时多会创建一个栈帧用于存储局部变量表、操作数栈、动态链表、方法出口等信息。
每一个方法从调用直至完成的过程，就对应着一个栈帧在虚拟机中入栈到出栈的过程。
局部变量表存放了编译期可知的各种基本数据类型和对象引用，所需内存空间在编译期确定

3、本地方法栈
同虚拟机栈，只不过本地方法栈位虚拟机使用到的native方法服务。
Sun HotSpot虚拟机把本地方法栈和虚拟机栈合二为一。

4、java堆
线程共享
主要用于分配对象实例和数组。

5、方法区
线程共享
用于存储已被虚拟机加载的类8息、常量、静态变量、即使编译后的代码等数据。

6、直接内存
直接内存并不是虚拟机运行时数据区的一部分。

总结：
大多数 JVM 将内存区域划分为 Method Area（Non-Heap）（方法区） ,Heap（堆） , Program Counter Register（程序计数器） ,   VM Stack（虚拟机栈，也有翻译成JAVA 方法栈的）,Native Method Stack  （ 本地方法栈 ），其中Method Area 和 Heap 是线程共享的  ，VM Stack，Native Method Stack  和Program Counter Register  是非线程共享的。为什么分为 线程共享和非线程共享的呢?请继续往下看。
首先我们熟悉一下一个一般性的 Java 程序的工作过程。一个 Java 源程序文件，会被编译为字节码文件（以 class 为扩展名），每个java程序都需要运行在自己的JVM上，然后告知 JVM 程序的运行入口，再被 JVM 通过字节码解释器加载运行。那么程序开始运行后，都是如何涉及到各内存区域的呢？
概括地说来，JVM初始运行的时候都会分配好 Method Area（方法区） 和Heap（堆） ，而JVM 每遇到一个线程，就为其分配一个 Program Counter Register（程序计数器） ,   VM Stack（虚拟机栈）和Native Method Stack  （本地方法栈）， 当线程终止时，三者（虚拟机栈，本地方法栈和程序计数器）所占用的内存空间也会被释放掉。这也是为什么我把内存区域分为线程共享和非线程共享的原因，非线程共享的那三个区域的生命周期与所属线程相同，而线程共享的区域与JAVA程序运行的生命周期相同，所以这也是系统垃圾回收的场所只发生在线程共享的区域（实际上对大部分虚拟机来说知发生在Heap上）的原因。

方法区用于存储JVM加载的类信息、常量、静态变量、即使编译器编译后的代码等数据，是线程隔离的(错误)
解析：方法区	和堆都是线程共享的。

### UseCompressedOops有什么作用为什么要使用

当你将你的应用从 32 位的 JVM 迁移到 64 位的 JVM 时，由于对象的指针从 32 位增加到了 64 位，因此堆内存会突然增加，差不多要翻倍。这也会对 CPU 缓存（容量比内存小很多）的数据产生不利的影响。因为，迁移到 64 位的 JVM 主要动机在于可以指定最大堆大小，通过压缩 OOP 可以节省一定的内存。通过 -XX:+UseCompressedOops 选项，JVM 会使用 32 位的 OOP，而不是 64 位的 OOP。

### 怎样通过Java程序来判断JVM是32位还是64位

你可以检查某些系统属性如 sun.arch.data.model 或 os.arch 来获取该信息。

### instanceofjava关键字

你在面月薪6000-8000的Java研发职位。面试官也知道JVM这么个大体概念，但知道的也不多。JVM这个概念本身就是“底层”。JVM有一条名为 instanceof 的指令，而Java源码编译到Class文件时会把Java语言中的 instanceof 运算符映射到JVM的 instanceof 指令上。

1.	instanceof 是javac能识别的一个关键字，对应到Token.INSTANCEOF的token类型。做词法分析的时候扫描到"instanceof"关键字就映射到了一个Token.INSTANCEOF token。jdk7u/jdk7u/langtools: 5c9759e0d341 src/share/classes/com/sun/tools/javac/parser/Token.java
2.	该编译器的抽象语法树节点有一个JCTree.JCInstanceOf类用于表示instanceof运算。做语法分析的时候解析到instanceof运算符就会生成这个JCTree.JCInstanceof类型的节点。jdk7u/jdk7u/langtools: 5c9759e0d341 src/share/classes/com/sun/tools/javac/parser/JavacParser.java term2Rest()
3.	中途还得根据Java语言规范对instanceof运算符的编译时检查的规定把有问题的情况找出来。
4.	到最后生成字节码的时候为JCTree.JCInstanceof节点生成instanceof字节码指令。jdk7u/jdk7u/langtools: 5c9759e0d341 src/share/classes/com/sun/tools/javac/jvm/Gen.java visitTypeTest()

### 堆和栈的区别
1. 栈是存储局部变量，基本数据和方法调用。
堆内存是存储Java中的对象。运行时期产生的而对象和数据会在堆中分配空间。是gc管理的最大的区域
2. 如果栈内存没有可用的空间存储方法调用和局部变量，JVM会抛出java.lang.StackOverFlowError。
而如果是堆内存没有可用的空间存储生成的对象，JVM会抛出java.lang.OutOfMemoryError。
3. Java的堆是一个运行时数据区，堆是由垃圾回收来负责的，堆的优势是可以动态地分配内存大小，生存期也不必事先告诉编译器，因为它是在运行时动态分配内存的。Java的垃圾收集器会自动收走这些不再使用的数据。但缺点是，由于要在运行时动态分配内存，存取速度较慢。
4. 栈的优势是，存取速度比堆要快，仅次于寄存器，栈数据可以共享。但缺点是，存在栈中的数据大小与生存期必须是确定的，缺乏灵活性。栈中主要存放一些基本类型的变量（,int, short, long, byte, float, double, boolean, char）和对象句柄。例如编译器先处理int a = 3；首先它会在栈中创建一个变量为a的引用，然后查找栈中是否有3这个值，如果没找到，就将3存放进来，然后将a指向3。
5. 栈中的数据共享的区别，和两个对象引用同时执行同一个堆中对象是不同的。通过一个对象的引用改变对象的数据，那么另一个对象的引用所指向的堆中的对象数据也会改变。而在栈中int a = 1;int b = 1; a改变不会影响吧的值。

### Class.forName的作用

调用该访问返回一个类名为指定字符串的类的对象。

### JSP4种域对象的查找方式

${pageScope .username} 作用范围: 当前页面
${requestScope.username} 作用范围:当前请求
${sessionScope.username} 作用范围:当前会话
${applicationScope.username} 作用范围:当前应用

### JSP的九大内置对象
 
| 名称 | 类 | 作用域 |
| :------ | :------ | :------ |
| request请求对象 | java.servlet.ServletRequest(接口)HttpServletRequest(实现类) | Request |
| response响应对象| javax.servlet.SrvletResponse(接口)HttpServletResponse(实现类) | Page |
| pageContext页面上下文对象 | javax.servlet.jsp.PageContext(实现类) | Page |
| session会话对象 | javax.servlet.http.HttpSession(接口)实现类(tomcat) | Session |
| application应用程序对象 | javax.servlet.ServletContext(接口)实现类(Tomcat中的实现类是ApplicationContext) | Application |
| out输出对象 | javax.servlet.jsp.JspWriter | Page |
| config配置对象 | javax.servlet.ServletConfig(在Tomcat中的实现类是StandardWrapper) | Page |
| page页面对象 | javax.lang.Object | Page |
| exception例外对象 | javax.lang.Throwable | Page |

总结
ServletConfig在Tomcat中的实现类是StandardWrapper
ServletContext在Tomcat中的实现类是ApplicationContext
Servlet能获取到的ServletConfig和ServletContext是StandardWrapper和ApplicationContext的Facade类
Facade是一种设计模式，作用是只开放部分的内容给使用者，详细请搜索Facade设计模式


### 什么是js
JavaScript 是属于网络的脚本语言！
JavaScript 被数百万计的网页用来改进设计、验证表单、检测浏览器、创建cookies，以及更多的应用。
JavaScript 是因特网上最流行的脚本语言。
JavaScript 很容易使用！你一定会喜欢它的！

### js的4种对象

js 中的内部对象包括Array、Boolean、Date、Function、Global、Math、Number、Object、RegExp、 String以及各种错误类对象，包括Error、EvalError、RangeError、ReferenceError、SyntaxError和 TypeError。
其中Global和Math这两个对象又被称为“内置对象”，这两个对象在脚本程序初始化时被创建，不必实例化这两个对象。

#### 内置对象

Number
JavaScript Number 对象是 一个数值包装器。您可以将其与 new 关键词结合使用，并将其设置为一个稍后要在 JavaScript 代码中使用的变量：
var myNumber = new Number(numeric value);
Boolean
Boolean 在尝试 用 JavaScript 创建任何逻辑时是必要的。Boolean 是一个 代表 true 或 false 值的对象。 Boolean 对象有多个值，这些值 相当于 false 值（0、 -0、null 或 "" [一个空字串]），未定义的 (NaN)，当然还有 false。所有其他布尔 值相当于 true 值。该对象可以 通过 new 关键词进行实例化，但通常是 一个被设为 true 或 false 值的变量：
var myBoolean = true;
String
JavaScript String 对象是 文本值的包装器。除了存储文本， String 对象包含一个属性和各种 方法来操作或收集有关文本的信息。与 Boolean 对象类似， String 对象不需要进行实例化 便能够使用。例如，您可以将一个变量设置为一个字符串， 然后 String 对象的所有属性或 方法都可用于该变量：
var myString = "My string";
Date
JavaScript Date 对象提供了一种方式 来处理日期和时间。您可以用许多不同的 方式对其进行实例化，具体取决于想要的结果。例如，您可以在没有参数的情况下对其进行实例化：
var myDate = new Date();
//或传递 milliseconds 作为一个参数：
var myDate = new Date(milliseconds);
//您可以将一个日期字符串作为一个参数传递：
var myDate = new Date(dateString);
//或者您可以传递多个参数来创建一个完整的日期：
var myDate = new Date(year, month, day, hours, minutes, seconds, milliseconds);
Array
JavaScript Array 对象是一个存储变量的变量：您可以用它一次在一个变量中存储多个值， 它有许多方法允许您操作或收集 有关它所存储的值的信息。尽管 Array 对象不差别对待值类型，但是 在一个单一数组中使用同类值是很好的做法。因此， 在同一数组中使用数字和字符串不是好的做法。所有 可用于 Array 对象的属性 都是只读的，这意味着它们的值不能从外部予以更改。
可用于 Array 对象的惟一属性 是 length。该属性返回 一个数组中的元素数目，通常在使用 循环迭代数组中的值时用到：
var myArray = new Array(1, 2, 3);
for(var i=0; i<myArray.length; i++) {
   document.write(myArray[i]);
}
Math
JavaScript Math 对象用于执行 数学函数。它不能加以实例化：您只能依据 Math 对象的原样使用它，在没有任何实例的情况下从该对象调用属性和 方法：
var pi = Math.PI;

#### 自定义对象
Person，Card…

function函数的类名对象
```javascript
function Student(id,name,sal){
	//this指向s引用
	this.id = id;
	this.name = name;
	this.sal = sal;
}
var s = new Student(1,"波波",7000);
document.write("编号:" + s.id + "<br/>");
document.write("姓名:" + s.name + "<br/>");
document.write("薪水:" + s.sal + "<br/>");
```
		
#### 浏览器对象
window，document，status，location，history…

```javascript
function myrefresh(){
			window.history.go(0);
		}
```

#### ActiveX对象
ActiveXObject("Microsoft.XMLHTTP")…

### 初始化js函数方式

1 浏览器对象调用

```javascript
window.onload=function(){
		alert(0);
		//var inputs = document.getElementsByTagName("input");
		//alert(inputs);
	}
```
2 jquery对象调用

```javascript
	$(function(){
		alert(0);
	})
```
3 写到标签中

```javascript
<body onload="myfunction()">
<html> <body onload="func1();func2();func3();"> </body> </html>

<script type="text/javascript">
Var myfunction = function (){
    /*用window.onload调用myfun()*/
alert("this window.onload");
} 　　
</script>
```

### javascript中获取标签元素的方式有哪些

#### getElementById

document.getElementById('demo') //demo是元素对应的ID

#### getElementsByTagName

document.getElementsByTagname('li')  //li是标签的名字
需要注意的是，该方法除了能被document对象调用之外，还可以被普通的元素调用。示例如下：
var demo = document.getElementById('demo');
var lis = demo.getElementsByTagname('li');

#### getElementsByClassName

除了通过指定标签获取元素外，DOM还提供了getElementsByClassName方法来获取指定class名的元素。不过由于该方法比较新，较老的浏览器还不支持，比如IE6。不过我们可以通过hack方式来弥补老浏览器缺陷。该方法调用的方式如下所示：
document.getElementsByClassName('demo')    //demo为元素指定的class名
和getElementsByTagname一样， 该方法除了能被document对象调用之外，还可以被普通的元素调用。
对于比较老的浏览器，比如IE6、7我们可以通过下面的hack方式来实现：

```javascript
function getElementsByClassName(node,classname){
        if(node.getElementsByClassName) {
            return node.getElementsByClassName(classname);
        }else {
            var results = [];
            var elems = node.getElementsByTagName("*");
            for(var i = 0; i < elems.length; i++){
                if(elems[i].className.indexOf(classname) != -1){
                    results[results.length] = elems[i];
                }
            }
            return results;
        }
    }   
```

### js中操作标签的class属性的关键字是什么

JavaScript中使用e.getAttribute('属性名')来取得属性的值，并且用e.setAttribute('属性名', '值')来设置属性值。
由于class属于JavaScript保留值，因此当我们要操作元素的class属性值时，直接使用e.getAttribute('class')和e.setAttribute('class', 'value')可能会遭遇浏览器兼容性问题
以下列表说明了上文提及的三种做法的浏览器兼容性测试：
•	e.className 能在IE、Mozilla(Firefox)、Opera和Safari正确运行
•	e.getAttribute('class')和e.setAttribute('class', 'value') 能在Mozilla(Firefox)和Opera中正确运行，在IE和Safari中则不能使用。
•	e.getAttribute('className') 在IE和Opera中正确运行，在Mozilla(Firefox)和Safari中则不能使用。

### 写出JavaScript后退到前一个页面的操作，与在浏览器上点击后退按钮相同

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>第一个页面</title>
</head>
<body>
	<p>
		这是第一个页面
	</p><br/>
	<a href="history2.html">下一个页面</a>
	<input id="return" type="button" value="前进" onclick="javascript:history.forward()" />
</body>
<script type="text/javascript"></script>
</html>
```

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>第二个页面</title>
</head>
<body>
	<p>
		这是第二个页面
	</p><br/>
	<input id="return" type="button" value="返回" onclick="javascript:history.back()" />
	<input id="return" type="button" value="返回" onclick="javascript:history.go(-1)" />
</body>
<script type="text/javascript"></script>
</html>
```

### ajax

AJAX 是 Asynchronous JavaScript And XML 的首字母缩写。
AJAX 并不是一种新的编程语言，而仅仅是一种新的技术，它可以创建更好、更快且交互性更强的 web 应用程序。
AJAX 使用 JavaScript 在 web 浏览器与 web 服务器之间来发送和接收数据。
通过在幕后与 web 服务器交换数据，而不是每当用户作出改变时重载整个 web 页面，AJAX 技术可以使网页更迅速地响应。

### servlet返回字符串到页面获取字符串
在一个servlet中：

```java
response.setContentType("text/html;charset=UTF-8");
PrintWriter pw = response.getWriter();
pw.write(tip);//写出到浏览器
pw.flush();
pw.close();
```
### ajax发送post请求

现有请求地址/getData，请求参数为id，参数值为1，请用jQuery通过Ajax发送POST请求，获取json数据，打印到控制台
action类

```java
import javax.servlet.http.HttpServletRequest;
import org.apache.struts2.ServletActionContext;
import com.opensymphony.xwork2.ActionSupport;

public class GetDataAction extends ActionSupport{
	
	private User user;
	public String getData(){//不能传参
		HttpServletRequest request = ServletActionContext.getRequest();
		System.out.println("进入action");
		String id = request.getParameter("id");
		user = new User();
		user.setId(id);
		user.setUserName("张三");
		System.out.println(user);
		return SUCCESS;
	}
	public User getUser() {
		return user;
	}
	
}
```

jsp页面

```html
<%@ page language="java" import="java.util.*" pageEncoding="utf-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<title>获取json对象</title>
<script type="text/javascript" src="../js/jquery-1.8.2.min.js"></script>
<head>
</head>
<body>
	<input type="button" value="获取数据" />
</body>
<script type="text/javascript">
	$(":button").click(function(){
		alert(0);
		$.post(
			"${pageContext.request.contextPath}/getData_getData.action?id=1",
			function(data,status,ajax){
					//ajax是json文本{"data":"",...}字符串,data是json文本的对象
					//在action中javabean要提供get方法ajax才能获得json格式的javabean(user)
				alert(ajax.responseText);//打印出{"data":"success","user":{"id":null,"userName":"张三"}}
				var user = data.user;
				alert(user);
				alert(user.id);
			}
		);
	});
</script>
</html>
```

### XML与json

xml扩展标记语言 (Extensible Markup Language, XML) ，用于标记电子文件使其具有结构性的标记语言，可以用来标记数据、定义数据类型，是一种允许用户对自己的标记语言进行定义的源语言。 XML使用DTD(document type definition)文档类型定义来组织数据;格式统一，跨平台和语言，早已成为业界公认的标准。
XML是标准通用标记语言 (SGML) 的子集，非常适合 Web 传输。XML 提供统一的方法来描述和交换独立于应用程序或供应商的结构化数据。++++++++++++++++++++++++++++++++++

JSON(JavaScript Object Notation)一种轻量级的数据交换格式，具有良好的可读和便于快速编写的特性。可在不同平台之间进行数据交换。JSON采用兼容性很高的、完全独立于语言文本格式，同时也具备类似于C语言的习惯(包括C, C++, C#, Java, JavaScript, Perl, Python等)体系的行为。这些特性使JSON成为理想的数据交换语言。
JSON基于JavaScript Programming Language , Standard ECMA-262 3rd Edition - December 1999 的一个子集。

XML的优点
　　A.格式统一，符合标准；
B.容易与其他系统进行远程交互，数据共享比较方便。
XML的缺点
　　A.XML文件庞大，文件格式复杂，传输占带宽；
　　B.服务器端和客户端都需要花费大量代码来解析XML，导致服务器端和客户端代码变得异常复杂且不易维护；
　　C.客户端不同浏览器之间解析XML的方式不一致，需要重复编写很多代码；
　　D.服务器端和客户端解析XML花费较多的资源和时间。

JSON的优点：
　　A.数据格式比较简单，易于读写，格式都是压缩的，占用带宽小；
　　B.易于解析，客户端JavaScript可以简单的通过eval()进行JSON数据的读取；
　　C.支持多种语言，包括ActionScript, C, C#, ColdFusion, Java, JavaScript, Perl, PHP, Python, Ruby等服务器端语言，便于服务器端的解析；
　　D.在PHP世界，已经有PHP-JSON和JSON-PHP出现了，偏于PHP序列化后的程序直接调用，PHP服务器端的对象、数组等能直接生成JSON格式，便于客户端的访问提取；
　　E.因为JSON格式能直接为服务器端代码使用，大大简化了服务器端和客户端的代码开发量，且完成任务不变，并且易于维护。
JSON的缺点
　　A.没有XML格式这么推广的深入人心和喜用广泛，没有XML那么通用性；
　　B.JSON格式目前在Web Service中推广还属于初级阶段。

### HTML加载顺序

```html
<html>
    <head>
        <!-- 引用外部JS文件 -->
        <script src="..........."></script>
        <!--引用外部css样式 -->
        <link href="............."/>
        <style>
               ..............
        </style>
        <script>
               ..............
        </script>
    </head>
    <body>
        <script>
               ..............
        </script>
    </body>
```

从上到下运行，先解析head标签中的代码，
（1）head标签中会包含一些引用外部文件的代码，从开始运行就会下载这些被引用的外部文件
当遇到script标签的时候浏览器暂停解析（不是暂停下载），将控制权交给JavaScript引擎（解释器）
如果`<script>`标签引用了外部脚本，就下载该脚本，否则就直接执行，执行完毕后将控制权交给浏览器渲染引擎
（2）当head中代码解析完毕，会开始解析body中的代码
如果此时head中引用的外部文件没有下载完，将会继续下载浏览器解析body代码中的元素，会按照head中声明一部分样式去解析
如果此时遇到body标签中的`<script>`，同样会将控制权交给JavaScript引擎来解析JavaScript解析完毕后将控制权交还给浏览器渲染引擎。
当body中的代码全部执行完毕、并且整个页面的css样式加载完毕后，css会重新渲染整个页面的html元素。
（3）按照之前的描述，`<script>`写到body标签内靠后比较好，因为JavaScript 会操作html元素， 如果在body加载完之前写JavaScript 会造成JavaScript 找不到页面元素
但是我们经常将`<script>`写到head中，body中不会有大量的js代码，body中的html代码结构会比较清晰
window.onload： 等待页面中的所有内容加载完毕之后才会执行$(document).ready()： 页面中所有DOM结构绘制完毕之后就能够执行
可以这样理解：window.onload 和 $(document).ready()/$(function(){}); 相当于写在body内最靠后的`<script>`代码段

### 引入css方式

#### 行内式

即在标记的style属性中设定CSS样式，这种方式本质上没有体现出CSS的优势，因此不推荐使用
例：
```html
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>Text Demo</title>
</head>
<body>
<h1 style=color:white;background-color=blue;>
This is a line of Text.
</h1>
</body>
</html>
```
#### 嵌入式
嵌入式则将对页面中各种元素的设置集中写在<head>和</head>之间，对于单一的网页，这种方法很方便。但是对于一个包含很多页面的网站，如果每个页面都以内嵌方式设置各自的样式，就失去了CSS带来的巨大优点，因此一个网站通常都是编写一个独立的CSS样式表文件，使用以下两种方式中的一种，引入HTML文档中。
例：
```html
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>Text Demo</title>
<style type="text/css">
h1{
color:white;
background-color:boue;
}
</style>
</head>
<body>
<h1>This is a line of Text.</h1>
<h1>This is another line of Text.</h1>
</body>
</html>
```

#### 导入式与链接式

导入式和链接式的目的都是将一个独立的CSS文件引入HTML文件，二者有相应的区别。
事实上，二者最大的区别在于链接式使用HTML的标记引入外部CSS文件，而使用导入式则是使用CSS的规则引入外部CSS文件。因此它们的语法也不同。
如果使用链接式，需要使用如下语句引入外部CSS文件。
```html
<link href="mystyle.css" rel="stylesheet" type="text/css" />
```
如果使用导入式，则需要使用如下语句。
```html
<style type="text/css">
@import"mystyle.css";
</style>
```

### 写出左浮动的css代码

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
</head>
<style>
#picture {
	float: left;
	width: 50px;
}
</style>
<body>
	<h3>一个浮动的图片</h3>
	<div id="pic">
		<img id="picture" src="image/斯诺登.jpg" alt="斯诺登" />
	</div>
	<div>
	<p>Iste quidem veteres inter ponetur honeste, qui vel mense brevi
		vel toto est iunior anno. Utor permisso, caudaeque pilos ut equinae
		paulatim vello unum, demo etiam unum, dum cadat elusus ratione ruentis
		acervi, qui redit in fastos et annis miraturque. Ennius et sapines et
		fortis et alter Homerus, ut critici dicunt, leviter curare videtur,
		quo promissa cadant et somnia Pythagorea.Naevius in manibus non est et
		mentibus haeret paene recens? Adeo sanctum est vetus omne poema.
		Ambigitur quotiens, sit prior, Pacuvius docti. Hos ediscit et hos arto
		stipata theatro spectat Roma potens; habet hos numeratque poetas ad
		nostrum tempus Livi scriptoris ab aevo, si nimis antique, si dure.</p>
	</div>
</body>
</html>
```

### 在多线程环境下，SimpleDateFormat 是线程安全的吗

不是，非常不幸，DateFormat 的所有实现，包括 SimpleDateFormat 都不是线程安全的，因此你不应该在多线程序中使用，除非是在对外线程安全的环境中使用，如 将 SimpleDateFormat 限制在 ThreadLocal 中。如果你不这么做，在解析或者格式化日期的时候，可能会获取到一个不正确的结果。因此，从日期、时间处理的所有实践来说，我强力推荐 joda-time 库。

### 判断JVM是大端还是小端

字节数组转为整形

```java
import java.io.ByteArrayInputStream;
import java.io.DataInputStream;
import java.io.IOException;

public class Test
{
	/**
	 * 将字节数组（byte[]）转为整形(int)
	 */
	public static void main(String[] args) throws IOException
	{
		byte[] byteAr = new byte[]{0x78,0x56,0x34,0x12};
		ByteArrayInputStream bais = new ByteArrayInputStream(byteAr);//字节数组输入流
		DataInputStream dis = new DataInputStream(bais);
		System.out.println(Integer.toHexString(dis.readInt()));
	}
}
// output: 78563412
```

结论：说明 JVM 是大端

整形转为字节数组

```java
import java.io.ByteArrayOutputStream;
import java.io.DataOutputStream;
import java.io.IOException;

public class Test
{
	public static void main(String[] args) throws IOException
	{
		/**
		 * 将整形(int)转为字节数组（byte[]）
		 */
		int a = 0x12345678;
		ByteArrayOutputStream baos = new ByteArrayOutputStream();//字节数组输出流
		DataOutputStream dos = new DataOutputStream(baos);
		dos.writeInt(a);
		byte[] b = baos.toByteArray();
		for(int i = 3; i >= 0; i--)
		{
			System.out.print(Integer.toHexString(b[i]));
		}
		System.out.println();
	}
}
// Output: 78563412
```
结论：说明 JVM 是大端


### final、finalize 和 finally 的不同之处

final 是一个修饰符，可以修饰变量、方法和类。如果 final 修饰变量，意味着该变量的值在初始化后不能被改变。finalize 方法是在对象被回收之前调用的方法，给对象自己最后一个复活的机会，但是什么时候调用 finalize 没有保证。finally 是一个关键字，与 try 和 catch 一起用于异常的处理。finally 块一定会被执行，无论在 try 块中是否有发生异常。
Java 中的编译期常量是什么？使用它又什么风险？
公共静态不可变（public static final ）变量也就是我们所说的编译期常量，这里的 public 可选的。实际上这些变量在编译时会被替换掉，因为编译器知道这些变量的值，并且知道这些变量在运行时不能改变。这种方式存在的一个问题是你使用了一个内部的或第三方库中的公有编译时常量，但是这个值后面被其他人改变了，但是你的客户端仍然在使用老的值，甚至你已经部署了一个新的jar。为了避免这种情况，当你在更新依赖 JAR 文件时，确保重新编译你的程序。




### 反射相关(Reflection)

#### 描述一下JVM加载class文件的原理机制? 

JVM中类的装载是由ClassLoader和它的子类来实现的,它负责在运行时查找和装入类文件的类。 
类装载器把一个类装入Java虚拟机中，要经过三个步骤来完成：装载、链接和初始化，其中链接又可以分成校验、准备、解析装载：查找和导入类或接口的二进制数据； 链接：执行下面的校验、准备和解析步骤，其中解析步骤是可以选择的； 校验：检查导入类或接口的二进制数据的正确性； 准备：给类的静态变量分配并初始化存储空间； 解析：将符号引用转成直接引用；初始化：激活类的静态变量,初始化Java代码和静态Java代码块

### Jquery知识

jQuery 是一个 JavaScript 函数库。“写更少的代码，做更多的事情”
jQuery 库包含以下特性：
•	HTML 元素选取
•	HTML 元素操作
•	CSS 操作
•	HTML 事件函数
•	JavaScript 特效和动画
•	HTML DOM 遍历和修改
•	AJAX
•	Utilities

#### 获取select下option的值

```javascript
<script type="text/javascript">
	$(document).ready(function() {
		$("#selecttest").change(function() { // www.jbxue.com
			//alert("hello");
			//alert($("#selecttest").attr("name"));
			//$("a").attr("href","xx.html");
			//window.location.href="xx.html";
			//alert($("#selecttest").val());
			var c=$("#selecttest option:selected").text();
			alert(c);
			$("#selecttest").attr("value", "2");
		});
	});
```

#### $(this) 和 this 关键字在 jQuery 中有何不同

这对于很多 jQuery 初学者来说是一个棘手的问题，其实是个简单的问题。$(this) 回一个 jQuery 对象，你可以对它调用多个 jQuery 方法，比如用 text() 获取文本，用val() 获取值等等。而 this 代表当前元素，它是 JavaScript 关键词中的一个，表示上下文中的当前 DOM 元素。你不能对它调用 jQuery 方法，直到它被 $() 函数包裹，例如 $(this)。

### <HTML>中的<!DOCTYPE>是什么

<!DOCTYPE> 声明必须是 HTML 文档的第一行，位于 <html> 标签之前。
<!DOCTYPE> 声明不是 HTML 标签；它是指示 web 浏览器关于页面使用哪个 HTML 版本进行编写的指令。

### Java 中的构造器链是什么

当你从一个构造器中调用另一个构造器，就是Java 中的构造器链。这种情况只在重载了类的构造器的时候才会出现。


### IO

![d](https://volc1612.gitee.io/blog/images/interview-question/d.jpg)
 
java.lang.Object
  java.io.InputStream(字节输入流) （abstract抽象类）
	AudioInputStream,(都是继承类)
	ByteArrayInputStream,
	FileInputStream(文件输入流), 
	FilterInputStream,
	InputStream,
	ObjectInputStream,
	PipedInputStream,
	SequenceInputStream,
	StringBufferInputStream
java.lang.Object
  java.io.OutputStream(字节输出流)
	ByteArrayOutputStream,
	FileOutputStream(文件输出流)
	FilterOutputStream,
	ObjectOutputStream,
	OutputStream,
	PipedOutputStream
java.lang.Object
  java.io.Reader(字符输入流)
	BufferedReader(字符读入缓冲区),
	CharArrayReader,
	FilterReader,
	InputStreamReader(字符流通向字节流桥梁),
	PipedReader,
	StringReader
java.lang.Object
  java.io.Writer(字符输出流)
BufferedWriter(字符写出缓冲区),
CharArrayWriter,
    FilterWriter,
OutputStreamWriter(字节流通向字符流桥梁), PipedWriter, PrintWriter, StringWriter

```java
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
public class JavaIOStudy {
    public static void main(String[] args) {
        //第一步:定位文件所在的位置并新建一个文件用于确定复制的文件的文件名
        File infile = new File("/Users/dxy/sync/infile.jpg");
        File outfile = new File("/Users/dxy/sync/outfile.jpg");
        try {
            //第二步:建立文件流管道
            FileInputStream fis = new FileInputStream(infile);
            FileOutputStream fos = new FileOutputStream(outfile);
            //第三步:操作文件流
            //第一种方式, 一次读一个字节, 会循环该文件的字节数
            int length1 = fis.available();
            for(int i=0;i<length1;i++){
                fos.write(fis.read());
            }
            //第二种方式, 一次读取多个字节
            //建一个byte数值用于临时成块存放字节文件,建议使用2的n次方,这样计算机的处理效率相对会高一些
            byte[] tmp = new byte[8192] ;
            //设置读取的次数
            int length2 = fis.available()/8192;
            for (int i = 0; i < length2; i++) {
                fis.read(tmp);
                fos.write(tmp);
            }
            //处理最后一次少读取的部分
            int size = fis.read(tmp) ;
            //第一个参数还是byte数组形成的临时缓冲区,第二个参数是从数组的哪里开始向文件写,第三个参数是写多少。
            fos.write(tmp, 0, size) ;
            //最后关闭文件流
            fis.close();
            fos.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

参考：http://www.runoob.com/java/java-files-io.html

![e](https://volc1612.gitee.io/blog/images/interview-question/e.png)
![f](https://volc1612.gitee.io/blog/images/interview-question/f.png)
 
参考：http://www.itzhai.com/java-based-notebook-io-flow-classification-and-detailed-description-of-various-classes-with-the-use-of-byte-stream-byte-stream-flow-filter.html#read-more

### 泛型

#### 泛型的好处

泛型能保证大型应用程序的类型安全和良好的维护性;
泛型好处：限定添加类型和消除强转转换的麻烦！

泛型是提供给javac编译器使用的，
利用反射穿透泛型限制 （暴力反射）
泛型能绝对保证集合中存入数据都是它限定的类型吗？先看下边的代码 

```java
import java.util.ArrayList;

public class Demo23 {
	public static void main(String[] args) {
		ArrayList<Integer> collection2 = new ArrayList<Integer>();   
		System.out.println(collection1.getClass()==collection2.getClass());   
		collection2.add（“真暴力”）；//这句会报错
		collection2.getClass().getMethod("add", Object.class).invoke(collection2, "真暴力");   
		System.out.println(collection2.get(0)); //结果却为真暴力  
		//已经限制集合中元素的类型为Integer，可用反射却能将String存入，为什么？ 这是因为泛型是给编译器用的，运行时就没有这些泛型信息了，这叫做“去泛型化”，所以可以通过反射，获取集合字节码加入非指定的类型。
	}
}
```

#### 泛型中的？通配符的扩展

泛型中的？通配符的扩展
  限定通配符的上边界：
正确：Vector<? extends Number> x = new Vector<Integer>();//Vector泛型的内容小于Number
错误：Vector<? extends Number> x = new Vector<String>();
  限定通配符的下边界：
正确：Vector<? super Integer> x = new Vector<Number>();//Vector泛型的内容要大于Integer
错误：Vector<? super Integer> x = new Vector<Byte>();


### GC

Java语言中一个显著的特点就是引入了垃圾回收机制，使c++程序员最头疼的内存管理的问题迎刃而解，它使得Java程序员在编写程序的时候不再需要考虑内存管理。由于有个垃圾回收机制，Java中的对象不再有“作用域”的概念，只有对象的引用才有“作用域”。垃圾回收可以有效的防止内存泄露，有效的使用空闲的内存。

参考：http://www.cnblogs.com/laoyangHJ/articles/java_gc.html

垃圾收集GC（Garbage Collection）是Java语言的核心技术之一，之前我们曾专门探讨过Java 7新增的垃圾回收器G1的新特性，但在JVM的内部运行机制上看，Java的垃圾回收原理与机制并未改变。垃圾收集的目的在于清除不再使用的对象。GC通过确定对象是否被活动对象引用来确定是否收集该对象。GC首先要判断该对象是否是时候可以收集。两种常用的方法是引用计数和对象引用遍历。
总的来说，可分为两大类

> * 引用计数收集器
引用计数是垃圾收集器中的早期策略。在这种方法中，堆中每个对象（不是引用）都有一个引用计数。当一个对象被创建时，且将该对象分配给一个变量，该变量计数设置为1。当任何其它变量被赋值为这个对象的引用时，计数加1（a = b,则b引用的对象+1），但当一个对象的某个引用超过了生命周期或者被设置为一个新值时，对象的引用计数减1。任何引用计数为0的对象可以被当作垃圾收集。当一个对象被垃圾收集时，它引用的任何对象计数减1。
优点：引用计数收集器可以很快的执行，交织在程序运行中。对程序不被长时间打断的实时环境比较有利。
缺点： 无法检测出循环引用。如父对象有一个对子对象的引用，子对象反过来引用父对象。这样，他们的引用计数永远不可能为0.

> * 跟踪收集器
早期的JVM使用引用计数，现在大多数JVM采用对象引用遍历。对象引用遍历从一组对象开始，沿着整个对象图上的每条链接，递归确定可到达（reachable）的对象。如果某对象不能从这些根对象的一个（至少一个）到达，则将它作为垃圾收集。在对象遍历阶段，GC必须记住哪些对象可以到达，以便删除不可到达的对象，这称为标记（marking）对象。
下一步，GC要删除不可到达的对象。删除时，有些GC只是简单的扫描堆栈，删除未标记的未标记的对象，并释放它们的内存以生成新的对象，这叫做清除（sweeping）。这种方法的问题在于内存会分成好多小段，而它们不足以用于新的对象，但是组合起来却很大。因此，许多GC可以重新组织内存中的对象，并进行压缩（compact），形成可利用的空间。
为此，GC需要停止其他的活动活动。这种方法意味着所有与应用程序相关的工作停止，只有GC运行。结果，在响应期间增减了许多混杂请求。另外，更复杂的 GC不断增加或同时运行以减少或者清除应用程序的中断。有的GC使用单线程完成这项工作，有的则采用多线程以增加效率。
实际上GC判断对象是否可达看的是强引用。更准确的描述是，一个对象存在强引用，必定是从其它强引用对象的本地变量，静态变量或者其它类似的地方直接引用过来的。换句话说，如果一堆对象通过某个不存活的对象“强引用”过来的话，它们会被一起回收掉。

#### 一些具体常用的垃圾收集算法

（1）标记－清除
这种收集器首先遍历对象图并标记可到达的对象，然后扫描堆栈以寻找未标记对象并释放它们的内存。这种收集器一般使用单线程工作并停止其他操作。并且，由于它只是清除了那些未标记的对象，而并没有对标记对象进行压缩，导致会产生大量内存碎片，从而浪费内存。
（2）标记－压缩
有时也叫标记－清除－压缩收集器，与标记－清除收集器有相同的标记阶段。在第二阶段，则把标记对象复制到堆栈的新域中以便压缩堆栈。这种收集器也停止其他操作。
（3）复制
这种收集器将堆栈分为两个域，常称为半空间。每次仅使用一半的空间，JVM生成的新对象则放在另一半空间中。GC运行时，它把可到达对象复制到另一半空间，从而压缩了堆栈。这种方法适用于短生存期的对象，持续复制长生存期的对象则导致效率降低。并且对于指定大小堆来说，需要两倍大小的内存，因为任何时候都只使用其中的一半。
 (4) 增量收集器
增量收集器把堆栈分为多个域，每次仅从一个域收集垃圾，也可理解为把堆栈分成一小块一小块，每次仅对某一个块进行垃圾收集。这会造成较小的应用程序中断时间，使得用户一般不能觉察到垃圾收集器正在工作。
（5）分代
复制收集器的缺点是：每次收集时，所有的标记对象都要被拷贝，从而导致一些生命周期很长的对象被来回拷贝多次，消耗大量的时间。而分代收集器则可解决这个问题，分代收集器把堆栈分为两个或多个域，用以存放不同寿命的对象。JVM生成的新对象一般放在其中的某个域中。过一段时间，继续存在的对象(非短命对象)将获得使用期并转入更长寿命的域中。分代收集器对不同的域使用不同的算法以优化性能。
（6）并行收集器
并行收集器使用某种传统的算法并使用多线程并行的执行它们的工作。在多CPU机器上使用多线程技术可以显著的提高java应用程序的可扩展性。
最后，贴出一个非常简单的跟踪收集器的例图，以便大家加深对收集器的理解：
![g](https://volc1612.gitee.io/blog/images/interview-question/g.png)

#### 使用垃圾收集器要注意的地方

下面将提出一些有关垃圾收集器要注意的地方，垃圾收集器知识很多，下面只列出一部分必要的知识：
（1）每个对象只能调用finalize( )方法一次。如果在finalize( )方法执行时产生异常（exception），则该对象仍可以被垃圾收集器收集。
（2）垃圾收集器跟踪每一个对象，收集那些不可触及的对象（即该对象不再被程序引用 了），回收其占有的内存空间。但在进行垃圾收集的时候，垃圾收集器会调用该对象的finalize( )方法（如果有）。如果在finalize()方法中，又使得该对象被程序引用(俗称复活了)，则该对象就变成了可触及的对象，暂时不会被垃圾收集了。但是由于每个对象只能调用一次finalize( )方法，所以每个对象也只可能 "复活 "一次。
（3）Java语言允许程序员为任何方法添加finalize( )方法，该方法会在垃圾收集器交换回收对象之前被调用。但不要过分依赖该方法对系统资源进行回收和再利用，因为该方法调用后的执行结果是不可预知的。
（4）垃圾收集器不可以被强制执行，但程序员可以通过调研System.gc方法来建议执行垃圾收集。记住，只是建议。一般不建议自己写System.gc，因为会加大垃圾收集工作量。
#### 详解Java GC的工作原理

概要: JVM内存结构由堆、栈、本地方法栈、方法区等部分组成，另外JVM分别对新生代和旧生代采用不同的垃圾回收机制。
参考：
http://www.cnblogs.com/ywl925/p/3925637.html


#### Java中WeakReference与SoftReference的区别

虽然 WeakReference 与 SoftReference 都有利于提高 GC 和 内存的效率，但是 WeakReference ，一旦失去最后一个强引用，就会被 GC 回收，而软引用虽然不能阻止被回收，但是可以延迟到 JVM 内存不足的时候

### 数组的排序

```java
int array[] = {33,55,22,11,-24,57,43,77,44,37,97,25,15}; 
//调用Arrays.sort()方法给数组排序，默认是升序 
Arrays.sort(array); 
//利用foreach方法，从数组输出元素 
for(int i : array) 
{ 
	System.out.print(i+" "); 
}
```

### 集合的排序

```java
System.out.println("调用Collections.sort(List<T> list)listCat2升序排序：");
Collections.sort(listCat1);
System.out.println("降序排列元素:");
Collections.sort(listCat1, Collections.reverseOrder());
System.out.println("Collections.reverse 从列表中最后一个元素开始输出:");
Collections.reverse(listCat1);
```

### URI、URL、URN

![h](https://volc1612.gitee.io/blog/images/interview-question/h.png)

URL的格式由下列三部分组成： 
第一部分:协议（或称为服务方式）； 
第二部分:存有该资源的主机IP地址（有时也包括端口号）； 
第三部分是主机资源地址路径。，如目录和文件名等。 
http://ws.webxml.com.cn/WebServices/MobileCodeWS.asmx?op=getMobileCodeInfo
第一部分：//第二部/第三部分/。第一部分和第二部分是不可缺少的，第三部分有时可以省略
http://www.aspxfans.com:8080/news/index.asp?boardID=5&ID=24618&page=1#name

从上面的URL可以看出，一个完整的URL包括以下几部分：

```
1.协议部分：该URL的协议部分为“http：”，这代表网页使用的是HTTP协议。在Internet中可以使用多种协议，如HTTP，FTP等等本例中使用的是HTTP协议。在"HTTP"后面的“//”为分隔符
2.域名(ip地址)部分：该URL的域名部分为“www.aspxfans.com”。一个URL中，也可以使用IP地址作为域名使用
3.端口部分：跟在域名后面的是端口，域名和端口之间使用“:”作为分隔符。端口不是一个URL必须的部分，如果省略端口部分，将采用默认端口
4.虚拟目录部分：从域名后的第一个“/”开始到最后一个“/”为止，是虚拟目录部分。虚拟目录也不是一个URL必须的部分。本例中的虚拟目录是“/news/”
5.文件名部分：从域名后的最后一个“/”开始到“？”为止，是文件名部分，如果没有“?”,则是从域名后的最后一个“/”开始到“#”为止，是文件部分，如果没有“？”和“#”，那么从域名后的最后一个“/”开始到结束，都是文件名部分。本例中的文件名是“index.asp”。文件名部分也不是一个URL必须的部分，如果省略该部分，则使用默认的文件名
6.锚部分：从“#”开始到最后，都是锚部分。本例中的锚部分是“name”。锚部分也不是一个URL必须的部分
7.参数部分：从“？”开始到“#”为止之间的部分为参数部分，又称搜索部分、查询部分。本例中的参数部分为“boardID=5&ID=24618&page=1”。参数可以允许有多个参数，参数与参数之间用“&”作为分隔符。
```

### 相对路径写/与不写/

jsp或servlet路径的相对路径是根据web应用程序而言的。
new Configraution().config(“hibernate.cfg.xml”);//不加/
request.getRequestDispatcher(“/user/a.jsp”)这个/相对于当前的web应用webapp， 其绝对地址就是：http://192.168.0.1/webapp/user/a.jsp。

### java常用的设计模式

#### 工厂方法模式

![i](https://volc1612.gitee.io/blog/images/interview-question/i.png)

```
举例如下：（我们举一个发送邮件和短信的例子）
首先，创建二者的共同接口：
[java] view plaincopy
1.	public interface Sender {  
2.	    public void Send();  
3.	}  
其次，创建实现类：
[java] view plaincopy
1.	public class MailSender implements Sender {  
2.	    @Override  
3.	    public void Send() {  
4.	        System.out.println("this is mailsender!");  
5.	    }  
6.	}  
[java] view plaincopy
1.	public class SmsSender implements Sender {  
2.	  
3.	    @Override  
4.	    public void Send() {  
5.	        System.out.println("this is sms sender!");  
6.	    }  
7.	}  
最后，建工厂类：
[java] view plaincopy
1.	public class SendFactory {  
2.	  
3.	    public Sender produce(String type) {  
4.	        if ("mail".equals(type)) {  
5.	            return new MailSender();  
6.	        } else if ("sms".equals(type)) {  
7.	            return new SmsSender();  
8.	        } else {  
9.	            System.out.println("请输入正确的类型!");  
10.	            return null;  
11.	        }  
12.	    }  
13.	}  
我们来测试下：
1.	public class FactoryTest {  
2.	  
3.	    public static void main(String[] args) {  
4.	        SendFactory factory = new SendFactory();  
5.	        Sender sender = factory.produce("sms");  
6.	        sender.Send();  
7.	    }  8.	}
```



#### 抽象工厂模式（Abstract Factory）

#### 单例模式（Singleton）
#### 建造者模式（Builder）

如下：一个Sender接口，两个实现类MailSender和SmsSender。最后，建造者类如下
```
1.	sender接口
2.	public interface Sender {  
3.	    public void Send();  
4.	}  
5.	public class Builder {  
6.	      
7.	    private List<Sender> list = new ArrayList<Sender>();  
8.	      
9.	    public void produceMailSender(int count){  
10.	        for(int i=0; i<count; i++){  
11.	            list.add(new MailSender());  
12.	        }  
13.	    }  
14.	      
15.	    public void produceSmsSender(int count){  
16.	        for(int i=0; i<count; i++){  
17.	            list.add(new SmsSender());  
18.	        }  
19.	    }  
20.	}
测试类：	
1.	public class Test {  
2.	  
3.	    public static void main(String[] args) {  
4.	        Builder builder = new Builder();  
5.	        builder.produceMailSender(10);  
6.	    }  7.	}
```


### String与StringBuffer

```java
String a = “a”;//创建一个对象
String b = “b” + “c”;//创建了一个对象”bc”
String c = new String(“d”);//创建了两个对象c对象和“d”
String d = new String(b)//创建一个对象对象，b存在于常量池，是初始化过的
d = d + “e”;//创建了两个对象，新的String类型d和“e”
String e = new String(b);//创建一个e对象
String f = new StringBuffer(“f”);//创建一个f对象，和String类型的“e”
f + “g”;//创建了一个对象”g”
```

### Java基本数据类型？int所占的字节数

```
byte char short int long float double boolean
```

字符与字节
ASCII码：一个英文字母（不分大小写）占一个字节的空间，一个中文汉字占两个字节的空间。
UTF-8编码：一个英文字符等于一个字节，一个中文（含繁体）等于二~四个字节。
Unicode编码：一个英文等于两个字节，一个中文（含繁体）等于两个字节。
符号：英文标点占一个字节，中文标点占两个字节。举例：英文句号“.”占1个字节的大小，中文句号“。”占2个字节的大小。
    C类型	           32	              64
    char	           1	               1
    short int	       2	               2
    int	               4	               4
    long int	       4	               8
    long long int	   8	               8
    char*	           4	               8
    float	           4 		           4
    double	           8	               8
需要说明一下的是指针类型存储的是所指向变量的地址，所以32位机器只需要32bit，而64位机器需要64bit

### 抽象

抽象类提供了继承的概念，它的出发点就是为了继承，否则它没有存在的任何意义。所以说定义的抽象类一定是用来继承的，同时在一个以抽象类为节点的继承关系等级链中，叶子节点一定是具体的实现类。

```java
abstract class Test2Abstract {//抽象类和普通类最大的区别就是不能让其他类创建这个抽象类的对象
	abstract void method();
	public void method2(){
		System.out.println("这是普通方法");
	}
	public static void mehtod3(){
		System.out.println("这是静态方法");
	}
	//public static abstract void mehtod4(){}//报错：抽象方法不能为static
	//abstract void method5(){}//报错：抽象方法不能有方法体
	public final void method6(){//抽象类中的方法可以为final，final方法不能在子类中重写(重载)[可以改变方法的参数个数，类型，和顺序]
		System.out.println("这个是final的方法");
	}
}
public class Test3 extends Test2Abstract{//普通类只能单继承抽象类，并且要实现抽象方法(继承和实现)
	@Override
	void method() {
		System.out.println("普通类继承了抽象类，并实现了抽象类中的抽象方法");
	}
	public static void main(String[] args) {
		Test3 test3 = new Test3();//抽象类只能让其他类继承，其他类不能创建抽象类的实例对象
		test3.mehtod3();
	}
	//public void method6(){}//报错：父类抽象类中的method6()为final类型不能重写
}
```

### 接口

在接口中只有常量，因为定义的变量，在编译的时候都会默认加上
总结：接口所有方法全是抽象方法只能 public abstract修饰 （默认public abstract修饰 ），属性默认public static final修饰。

### Java语言中的修饰符

主要分为以下两类：
•	访问修饰符
•	非访问修饰符

```
•	//Final类
•	//Final类不能被继承，没有类能够继承final类的任何特性。
•	//实例：
•	public final class Test {
•		   // 类体•	}
```

基本类型与引用类型的区别
基本类型在声明其变量时就为它分配了空间。
在Java中，除了基本数据类型之外，其他的都是引用类型。

```java
int  f( int  b)  /* b为形参 */  
{  return  b;} 
main() 
{ int  a = 1 ; 
printf( " %d " ,f(a)); /* a为实参 */  
}
```

例如，形参改变了，但是实参没有被改变。代码如下

```java
import java.util.*;  
public class Student  
{  
   private String name;  
   private int age;  
   public Student(String name,int age)  
   {  
      this.name=name;  
      this.age=age;  
   }  
   public void printInfo()  
   {  
      System.out.println("Name:"+name+" age:"+age);
    }
   public static void change(Student student) //student为形参，将s实参赋值给student
   {
      //新增的输出操作
      if(student!=null)  
      {  
         student.printInfo();  
      }  
      Student stu=new Student("Lucy",26);  
      student=stu;  
      student.printInfo();  
    }  
   public static void main(String[]args)  
   {  
      Student s=new Student("Lily",25);  
      s.printInfo();  
      change(s);  //change(Student student)的形参student改变了。
      s.printInfo();  //实参s并没有改变
    }  
}
```

### 什么是值传递，什么是引用传递

1.值传递只传递一个对象的拷贝，本身的数据不会发生改变，一般java中8种基本类型都是值传递。 

```java
public class TransferTest {
    public static void main(String[] args) {
        int num = 1;
        System.out.println("changeNum()方法调用之前：num = " + num);
        changeNum(num);
        System.out.println("changeNum()方法调用之后：num = " + num);
    }

    public static void changeNum(int x) {
        System.out.println("x = " + x);
        x = 2;
        System.out.println("x = " + x);
    }
}
```
console
```
changeNum()方法调用之前：num = 1
x = 1
x = 2
changeNum()方法调用之后：num = 1
```

这个传递过程的示意图如下：
![j](https://volc1612.gitee.io/blog/images/interview-question/j.png)

num作为参数传递给changeNum()方法时，是将内存空间中num所指向的那个存储单元中存放的值1传递给了changeNum()方法中的x变量，而这个x变量也在内存空间中分配了一个存储单元，这个时候，就把num的值1传递给了x的这个存储单元中。此后，在changeNum()方法中对x的一切操作都是针对x所指向的这个存储单元，与num所指向的那个存储单元没有关系了！

所以，在changeNum()方法调用之后，num所指向的存储单元的值还是没有发生变化，这就是所谓的“按值传递”！按值传递的精髓是：传递的是存储单元中的内容，而不是存储单元的引用！

当一个对象被当作参数传递到一个方法后，在此方法内可以改变这个对象的属性，那么这里到底是「按值传递」还是「按引用传递」? 
答：是按值传递。Java 语言的参数传递只有「按值传递」。当一个实例对象作为参数被传递到方法中时，参数的值就是该对象的引用的一个副本。指向同一个对象，对象的内容可以在被调用的方法内改变，但对象的引用(不是引用的副本) 是永远不会改变的。

Java的参数传递，不管是基本数据类型还是引用类型的参数，都是按值传递，没有按引用传递！

2.引用传递，将对象的引用传递过去，改变这个引用也会改变对象本身的数据。

```java
public class TransferTest2 {
    public static void main(String[] args) {
        Person p1 = new Person();
        System.out.println(p1);
        change(p1);
        System.out.println(p1);
    }

    public static void change(Person p2) {
        p2 = new Person();
    }
}

/**
 * Person类
 */
class Person {}
```
console
```
Person@41629346
Person@41629346
```
可以看出两次打印person的地址值是一样的，即调用完change() 方法之后，person变量并没有发生改变。

这个传递过程的示意图如下：

![k](https://volc1612.gitee.io/blog/images/interview-question/k.png)

当执行到第3行代码时，程序在堆内存中开辟了一块内存空间用来存储Person类的实例对象，同时在栈内存中开辟了一个存储单元用来存储该实例对象的引用，即上图中person指向的存储单元。
当执行到第5行代码时，person作为参数传递给change()方法，需要注意的是：person将自己存储单元的内容传递给了change()方法的p变量！此后，在change()方法中对p的一切操作都是针对p所指向的存储单元，与person所指向的那个存储单元没有关系了！


基本类型（原始数据类型）在传递参数时都是按值传递，而封装类型是按引用传递的


### 为什么要重写equal方法

Object类中的equals方法比较两个对象的原则是：两个对象是否在内存中同一个位置及比较的是地址(与 == 的作用相同)，并不是基于对象的特征来进行比较的，因此这也是为什么需要重写equals方法的原因。

### 为什么要重写hashcode

当equals被重写时，通常有必要重写 hashCode 方法，以维护 hashCode 方法的常规协定，该协定声明相等对象必须具有相等的哈希码。如下：
(1)当obj1.equals(obj2)为true时，obj1.hashCode() == obj2.hashCode()必须为true 
(2)当obj1.hashCode() == obj2.hashCode()为false时，obj1.equals(obj2)必须为false
如果不重写equals，那么比较的将是对象的引用是否指向同一块内存地址，重写之后目的是为了比较两个对象的value值是否相等。特别指出利用equals比较八大包装对象
（如int，float等）和String类（因为该类已重写了equals和hashcode方法）对象时，默认比较的是值，在比较其它自定义对象时都是比较的引用地址
hashcode是用于散列数据的快速存取，如利用HashSet/HashMap/Hashtable类来存储数据时，都是根据存储对象的hashcode值来进行判断是否相同的。
这样如果我们对一个对象重写了euqals，意思是只要对象的成员变量值都相等那么euqals就等于true，但不重写hashcode，那么我们再new一个新的对象，
当原对象.equals（新对象）等于true时，两者的hashcode却是不一样的，由此将产生了理解的不一致，如在存储散列集合时（如Set类），将会存储了两个值一样的对象，
导致混淆，因此，就也需要重写hashcode()


示例：

```java
import java.util.Collection;
import java.util.HashSet;

public class HelloWorld {
    public static void main(String[] args) {
        /*
        Collection c = new HashSet();
        c.add("hello");
        c.add(new Name("f1","l1"));
        c.add(new Integer(100));
        c.remove("hello"); 
        c.remove(new Integer(100));
        System.out.println(c.remove(new Name("f1","l1")));
        */
        Name n1 = new Name("01");
        Name n2 = new Name("01");

        Collection c = new HashSet();
        c.add(n1);
        System.out.println("------------");
        c.add(n2);
        System.out.println("------------");
        System.out.println(n1.equals(n2));
        System.out.println("------------");
        System.out.println(n1.hashCode());
        System.out.println(n2.hashCode());
        System.out.println(c);
    }


}

class Name {
    private String id;

    public Name(String id) {
        this.id = id;
    }

    public String toString() {
        return this.id;
    }

    /**
     * 重写equal方法，根据Name属性id，如果相同则返回true，不同则返回false
     * @param obj
     * @return
     */
    public boolean equals(Object obj) {
        if (obj instanceof Name) {
            Name name = (Name) obj;
            System.out.println("equal" + name.id);
            return (id.equals(name.id));
        }
        return super.equals(obj);
    }

    /**
     * 重写了hashcode方法也是根据Name属性的id值的hashCode
     * equals和hashcode方法的判断属性要一致
     * @return
     */
    public int hashCode() {
        Name name =  this;
        System.out.println("Hash" + name.id);
        return id.hashCode();

    }
}
```

### hashCode()和equals()的区别
1.equals() 用于判断两个对象的数据、内容是否一致。 
2.hashCode()用于生成一个哈希码(相应对象整型的 hash 值)，便于HashMap使用，系统默认实现是计算对象的内存地址，转换成一个整数。 它们之间没什么联系，两个相等的对象生成的HASH码应该是相等，但是HASH码相等并不能保证两个对象相等。关键在于你对相等的定义（也就是重写equals函数），与此同时，也应该重写hashCode函数保证上述关系。

### collection

java.util.Collection [I]
+--java.util.List [I]
   +--java.util.ArrayList [C]
   +--java.util.LinkedList [C]
   +--java.util.Vector [C]
      +--java.util.Stack [C]
+--java.util.Set [I]
   +--java.util.HashSet [C]
   +--java.util.SortedSet [I]
      +--java.util.TreeSet [C]

java.util.Map [I]
+--java.util.SortedMap [I]
   +--java.util.TreeMap [C]
+--java.util.Hashtable [C]
+--java.util.HashMap [C]
+--java.util.LinkedHashMap [C]
+--java.util.WeakHashMap [C]
参考：http://android.blog.51cto.com/268543/400557

![l](https://volc1612.gitee.io/blog/images/interview-question/l.png)
![m](https://volc1612.gitee.io/blog/images/interview-question/m.png)
![n](https://volc1612.gitee.io/blog/images/interview-question/n.png)　	

![o](https://volc1612.gitee.io/blog/images/interview-question/o.png)

#### Map集合取值方式

```java
public static void testMap(){  
        //Map 无序key值不重复  
        Map<String, String> map = new HashMap<String, String>();  
        map.put("zhangsan", "我是张三");  
        map.put("devin", "我是dw");  
        map.put("haha", "我是哈哈");  
          
        //遍历方法一 map.keySet + for  
        for(String key : map.keySet()){  
            System.out.println("key="+ key + ",value=" + map.get(key));  
        }  
          
        //遍历方法二map.keySet +Iterator  
        Iterator<String> iterator = map.keySet().iterator();  
        while(iterator.hasNext()){  
            String key1 = iterator.next().toString();  
            System.out.println("key="+ key1 + ",value=" + map.get(key1));  
        }  
          
        //遍历方式三 map.values  
        for (String key2 : map.values()) {    
            System.out.println("value=" + key2);  
        }  
          
        //遍历方式四  map.entrySet + for  
        for(Map.Entry<String,String> entry : map.entrySet()){  
            System.out.println("key="+ entry.getKey() + ",value=" + entry.getValue());  
        }  
          
        //遍历方式五map.entrySet() + Iterator  
        Iterator <Map.Entry<String,String>> entry = map.entrySet().iterator();  
        while(entry.hasNext()){  
            Map.Entry<String, String> ent = entry.next();  
            System.out.println("key="+ ent.getKey() + ",value=" + ent.getValue());  
        } 
	}
```

#### Java 中的 HashSet，内部是如何工作的

HashSet 的内部采用 HashMap来实现。由于 Map 需要 key 和 value，所以所有 key 的都有一个默认 value。类似于 HashMap，HashSet 不允许重复的 key，只允许有一个null key，意思就是 HashSet 中只允许存储一个 null 对象。

#### Hashtable 和 HashMap之间的区别

1. 都实现了Map接口，Hashtable是线程安全的，HashMap非线程安全，hashMap速度较快一些。
    2. HashMap的迭代器(Iterator)是fail-fast迭代器，而Hashtable的enumerator迭代器不是fail-fast的。所以当有其它线程改变了HashMap的结构（增加或者移除元素），将会抛出ConcurrentModificationException，但迭代器本身的remove()方法移除元素则不会抛出ConcurrentModificationException异常。但这并不是一个一定发生的行为，要看JVM。这条同样也是Enumeration和Iterator的区别。
    3. HashMap允许null键值，Hashtable不允许有null值。
    4. HashMap把Hashtable的contains方法去掉了，改成containsvalue和containsKey。因为contains方法容易让人引起误解。
    5. Hashtable继承自Dictionary类，而HashMap是Java1.2引进的Map interface的一个实现。

#### 说出ArrayList,Vector, LinkedList的存储性能和特性

ArrayList和Vector都是使用数组方式存储数据，此数组元素数大于实际存储的数据以便增加和插入元素，它们都允许直接按序号索引元素，但是插入元素要涉及数组元素移动等内存操作，所以索引数据快而插入数据慢，Vector由于使用了synchronized方法（线程安全），通常性能上较ArrayList差，而LinkedList使用双向链表实现存储，按序号索引数据需要进行前向或后向遍历，但是插入数据时只需要记录本项的前后项即可，所以插入速度较快。

ArrayList数组方式存储，可以扩容1.5倍+1，按序号索引，查询更新快，插入删除慢，非线程安全
Vector  数组方法存储，可以扩容2倍，按序号索引，查询更新快，插入删除慢，线程安全
LinkedList双向链表存储，不可扩容，移动指针查询，查询更新慢，插入删除快，非线程安全

#### ArrayList 和 HashMap 的默认大小是多数

在 Java 7 中，ArrayList 的默认大小是 10 个元素，HashMap 的默认大小是16个元素（必须是2的幂）。这就是 Java 7 中 ArrayList 和 HashMap 类的代码片段：


#### Java 中，Comparator 与 Comparable 有什么不同

Comparable 接口用于定义对象的自然顺序，而 comparator 通常用于定义用户定制的顺序。Comparable 总是只有一个，但是可以有多个 comparator 来定义对象的顺序。

### 为什么在重写equals方法的时候需要重写hashCode方法

因为有强制的规范指定需要同时重写 hashcode 与 equal 是方法，许多容器类，如 HashMap、HashSet 都依赖于 hashcode 与 equals 的规定。

### session的特点

(1)不是线程安全的，因此在设计软件架构时，应该避免多个线程共享同一个Session实例。 
(2)Session实例是轻量级的，所谓轻量级是指它的创建和销毁不需要消耗太多的资源。这意味着在程序中可以经常创建或销毁Session对象，例如为每个客户请求分配单独的Session实例，或者为每个工作单元分配单独的Session实例。 
(3)在Session 中，每个数据库操作都是在一个事务(transaction)中进行的，这样就可以隔离开不同的操作（甚至包括只读操作）。

### 简述cookie与session作用、区别、应用范围、session的工作原理
#### Cookie原理
				1）服务器创建cookie对象，把会话数据存储到cookie对象中。
						new Cookie("name","value");
				2）	服务器发送cookie信息到浏览器
						response.addCookie(cookie);

						举例： set-cookie: name=eric  (隐藏发送了一个set-cookie名称的响应头)
				3）浏览器得到服务器发送的cookie，然后保存在浏览器端。
				4）浏览器在下次访问服务器时，会带着cookie信息
					    举例： cookie: name=eric  (隐藏带着一个叫cookie名称的请求头)
				5）服务器接收到浏览器带来的cookie信息
						request.getCookies();

#### session工作原理
代码解读：HttpSession session = request.getSession();
			1）第一次访问创建session对象，给session对象分配一个唯一的ID，叫JSESSIONID
					new HttpSession();
			2）把JSESSIONID作为Cookie的值发送给浏览器保存
					Cookie cookie = new Cookie("JSESSIONID", sessionID);
					response.addCookie(cookie);
			3）第二次访问的时候，浏览器带着JSESSIONID的cookie访问服务器
			4）服务器得到JSESSIONID，在服务器的内存中搜索是否存放对应编号的session对象。
					if(找到){
						return map.get(sessionID);
					}
					Map<String,HttpSession>]
					<"s001", s1>
					<"s001,"s2>
			5）如果找到对应编号的session对象，直接返回该对象
			6）如果找不到对应编号的session对象，创建新的session对象，继续走1的流程
	
			结论：通过JSESSION的cookie值在服务器找session对象！！！！！

cookie是一种客户端的机制
	cookie:域：google.com称之为域
			后面带的url称之为路径，路径和域共同组成了cookie
	使用javascript脚本语言和windos.open打开的窗口会与原窗口使用用一个cookie
session是一种服务端的，当程序需要为某个客户端的请求创建一个session服务器，首先按照客户端的sessionid把这个session检索出来，如果找不到sessionid就会创建一个新的session并返回给客户端保存，保存这个客户端的sessionid可以采用cookie

Cookie类：用于存储会话数据

				1）构造Cookie对象
					Cookie(java.lang.String name, java.lang.String value)
				2）设置cookie
					void setPath(java.lang.String uri)   ：设置cookie的有效访问路径
					void setMaxAge(int expiry) ： 设置cookie的有效时间
					void setValue(java.lang.String newValue) ：设置cookie的值
				3）发送cookie到浏览器端保存
					void response.addCookie(Cookie cookie)  : 发送cookie
				4）服务器接收cookie
					Cookie[] request.getCookies()  : 接收cookie
cookie案例:
显示用户上次访问的时间
查看用户浏览器过的商品
5天内自动登录:
	用户名和密码(MD5加密返回保存在cookie中)
	
#### cookie 和session 的区别

1、cookie数据存放在客户的浏览器上，session数据放在服务器上。
2、cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗
   考虑到安全应当使用session。
3、session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能
   考虑到减轻服务器性能方面，应当使用COOKIE。
4、单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie。
5、所以个人建议：
   将登陆信息等重要信息存放为SESSION
   其他信息如果需要保留，可以放在COOKIE中

### 进程概念
进程是表示资源分配的基本单位，又是调度运行的基本单位

### 线程概念
线程是进程中执行运算的最小单位，亦即执行处理机调度的基本单位

#### 线程调度方式

     线程调度室指系统为线程分配处理器使用权的古城，主要的调度方式有两种：协同式和抢占式线程调度。

协同式线程调度
     使用协同式的线程调度，线程的执行时间由线程本身来控制，线程把自己的工作执行完了后，要主动通知系统切换到另一个线程上去。协同式线程调度的最大优点是实现简单，而且由于线程要把自己的事情干完后才会进行线程切换，因此没有什么线程同步的问题。它的缺点很明显：线程执行时间不可控制，有可能出现线程饥饿、永久阻塞等问题。比如，如果一个线程编写有问题，一直不告知系统进行线程切换，那么程序就会一直阻塞在那里。
    抢占式线程调度
     使用抢占式的线程调度，每个线程将由系统来分配执行时间，线程的切换不由线程本身来决定（在Java中，Thread.yield（）可以让出执行时间，但是要获得执行时间的话，线程本身是没有办法的）。在这种实现线程调度的方式下，线程的执行时间是系统可控的，也不会因为一个线程而导致整个进程阻塞。Java使用的线程调度方式就是抢占式调度。另外，抢占式线程调度也与线程优先级有关，不过线程优先级并不是很靠谱，原因是Java的线程是被映射到系统的原生线程上来实现的，所以线程调度最终还是由操作系统说了算，虽然现在好恩多操作系统都有提供线程优先级的概念，但是并不见得能与Java线程的优先级一一对应。因此，不能太依赖优先级。
	 
#### java多线程中的伪共享

![p](https://volc1612.gitee.io/blog/images/interview-question/p.png)
图1
当多线程修改互相独立的变量时，如果这些变量共享同一个缓存行，就会无意中影响彼此的性能，这就是伪共享

图1说明了伪共享的问题。在核心1上运行的线程想更新变量X，同时核心2上的线程想要更新变量Y。不幸的是，这两个变量在同一个缓存行中。每个线程都要去竞争缓存行的所有权来更新变量。如果核心1获得了所有权，缓存子系统将会使核心2中对应的缓存行失效。当核心2获得了所有权然后执行更新操作，核心1就要使自己对应的缓存行失效。这会来来回回的经过L3缓存，大大影响了性能。如果互相竞争的核心位于不同的插槽，就要额外横跨插槽连接，问题可能更加严重。

### 继承thread类与实现runnable接口的区别

实现Runnable接口比继承Thread类所具有的优势：
1）：适合多个相同的程序代码的线程去处理同一个资源
2）：可以避免java中的单继承的限制
3）：增加程序的健壮性，代码可以被多个线程共享，代码和数据独立

### 实现多线程方法

1 实现Runnable接口
2 集成thread类
3 实现Callable(实现有返回结果的多线程)
```java
import java.util.concurrent.*;
import java.util.Date;
import java.util.List;
import java.util.ArrayList;

/**
* Java线程：有返回值的线程
* 
* @author wb_qiuquan.ying
*/
@SuppressWarnings("unchecked")
public class Test {
public static void main(String[] args) throws ExecutionException,
    InterruptedException {
   System.out.println("----程序开始运行----");
   Date date1 = new Date();

   int taskSize = 5;
   // 创建一个线程池
   ExecutorService pool = Executors.newFixedThreadPool(taskSize);
   // 创建多个有返回值的任务
   List<Future> list = new ArrayList<Future>();
   for (int i = 0; i < taskSize; i++) {
    Callable c = new MyCallable(i + " ");
    // 执行任务并获取Future对象
    Future f = pool.submit(c);
    // System.out.println(">>>" + f.get().toString());
    list.add(f);
   }
   // 关闭线程池
   pool.shutdown();

   // 获取所有并发任务的运行结果
   for (Future f : list) {
    // 从Future对象上获取任务的返回值，并输出到控制台
    System.out.println(">>>" + f.get().toString());
   }

   Date date2 = new Date();
   System.out.println("----程序结束运行----，程序运行时间【"
     + (date2.getTime() - date1.getTime()) + "毫秒】");
}
}
```
```java
class MyCallable implements Callable<Object> {
private String taskNum;

MyCallable(String taskNum) {
   this.taskNum = taskNum;
}

public Object call() throws Exception {
   System.out.println(">>>" + taskNum + "任务启动");
   Date dateTmp1 = new Date();
   Thread.sleep(1000);
   Date dateTmp2 = new Date();
   long time = dateTmp2.getTime() - dateTmp1.getTime();
   System.out.println(">>>" + taskNum + "任务终止");
   return taskNum + "任务返回运行结果,当前任务时间【" + time + "毫秒】";
}
}
```

### 线程工作流程

1. 创建状态 
使用new运算符创建一个线程后,该线程仅仅是一个空对象,系统没有分配资源,称该线程处于创建状态(new thread)。 
2. 可运行状态 
使用start()方法启动一个线程后,系统为该线程分配了除CPU外的所需资源,使该线程处于可运行状态(Runnable)。 
3. 运行中状态 
Java运行系统通过调度选中一个Runnable的线程,使其占有CPU并转为运行中状态(Running).此时,系统真正执行线程的run()方法。 
4. 阻塞状态 
一个正在运行的线程因某种原因不能继续运行时,进入阻塞状态(Blocked)。 
	1>线程通过调用sleep方法进入睡眠状态；
        2>线程调用一个在I/O上被阻塞的操作，即该操作在输入输出操作完成之前不会返回到它的调用者；
        3>线程试图得到一个锁，而该锁正被其他线程持有；
        4>线程在等待某个触发条件；
5. 死亡状态 
线程结束后是死亡状态(Dead)。

### volatile

用volatile修饰后的变量不允许有不同于“主”内存区域的变量拷贝。换句话说，一个变量经 volatile修饰后在所有线程中必须是同步的；任何线程中改变了它的值，所有其他线程立即获取到了相同的值。理所当然的，volatile修饰的变量存取时比一般变量消耗的资源要多一点，因为线程有它自己的变量拷贝更为高效。
volatile关键字相信了解Java多线程的读者都很清楚它的作用。volatile关键字用于声明简单类型变量，如int、float、 boolean等数据类型。如果这些简单数据类型声明为volatile，对它们的操作就会变成原子级别的。但这有一定的限制。例如，下面的例子中的n就不是原子级别的：

```java  
public   class  JoinThread  extends  Thread  
{  
     public   static volatile int  n  =   0 ;  
    public   void  run()  
    {  
         for  ( int  i  =   0 ; i  <   10 ; i ++ )  
             try   
        {  
                n  =  n  +   1 ;  
                sleep( 3 );  //  为了使运行结果更随机，延迟3毫秒   
  
            }  
             catch  (Exception e)  
            {  
            }  
    }  
  
     public   static   void  main(String[] args)  throws  Exception  
    {  
  
        Thread threads[]  =   new  Thread[ 100 ];  
         for  ( int  i  =   0 ; i  <  threads.length; i ++ )  
             //  建立100个线程   
            threads[i]  =   new  JoinThread();  
         for  ( int  i  =   0 ; i  <  threads.length; i ++ )  
             //  运行刚才建立的100个线程   
            threads[i].start();  
         for  ( int  i  =   0 ; i  <  threads.length; i ++ )  
             //  100个线程都执行完后继续   
            threads[i].join();  
        System.out.println( " n= "   +  JoinThread.n);  
    }  
}   
```

如果对n的操作是原子级别的，最后输出的结果应该为n=1000，而在执行上面积代码时，很多时侯输出的n都小于1000，这说明n=n+1不是原子级别的操作。原因是声明为volatile的简单变量如果当前值由该变量以前的值相关，那么volatile关键字不起作用，也就是说如下的表达式都不是原子操作： 

n  =  n  +   1 ; 
n ++ ;
如果要想使这种情况变成原子操作，需要使用synchronized关键字，如上的代码可以改成如下的形式：

```java  
public   class  JoinThread  extends  Thread  
{  
     public   static int  n  =   0 ;  
  
     public static   synchronized   void  inc()  
    {  
        n ++ ;  
    }  
     public   void  run()  
    {  
         for  ( int  i  =   0 ; i  <   10 ; i ++ )  
             try   
            {  
                inc();  //  n = n + 1 改成了 inc();   
                sleep( 3 );  //  为了使运行结果更随机，延迟3毫秒   
  
            }  
             catch  (Exception e)  
            {  
            }  
    }  
  
     public   static   void  main(String[] args)  throws  Exception  
    {  
  
        Thread threads[]  =   new  Thread[ 100 ];  
         for  ( int  i  =   0 ; i  <  threads.length; i ++ )  
             //  建立100个线程   
            threads[i]  =   new  JoinThread();  
         for  ( int  i  =   0 ; i  <  threads.length; i ++ )  
             //  运行刚才建立的100个线程   
            threads[i].start();  
         for  ( int  i  =   0 ; i  <  threads.length; i ++ )  
             //  100个线程都执行完后继续   
            threads[i].join();  
        System.out.println( " n= "   +  JoinThread.n);  
    }  
}
```

上面的代码将n=n+1改成了inc()，其中inc方法使用了synchronized关键字进行方法同步。因此，在使用volatile关键字时要慎重，并不是只要简单类型变量使用volatile修饰，对这个变量的所有操作都是原来操作，当变量的值由自身的上一个决定时，如n=n+1、n++ 等，volatile关键字将失效，只有当变量的值和自身上一个值无关时对该变量的操作才是原子级别的，如n = m + 1，这个就是原级别的。所以在使用volatile关键时一定要谨慎，如果自己没有把握，可以使用synchronized来代替volatile。 

### 什么是线程局部变量

线程局部变量是局限于线程内部的变量，属于线程自身所有，不在多个线程间共享。Java 提供 ThreadLocal 类来支持线程局部变量，是一种实现线程安全的方式。但是在管理环境下（如 web 服务器）使用线程局部变量的时候要特别小心，在这种情况下，工作线程的生命周期比任何应用变量的生命周期都要长。任何线程局部变量一旦在工作完成后没有释放，Java 应用就存在内存泄露的风险。

### HTTP协议

超文本传输协议（HTTP，HyperText Transfer Protocol)是互联网上应用最为广泛的一种网络协议。所有的WWW文件都必须遵守这个标准。http请求要依赖socket。
请求头 包含get方式时url中参数
请求行
空格
请求体 包含post方式时提交的参数
HTTP的请求：

```
HTTP/1.1 200 OK                --响应行
Server: Apache-Coyote/1.1         --响应头（key-vaule）
Content-Length: 24 
Date: Fri, 30 Jan 2015 01:54:57 GMT
                                   --一个空行
this is hello servlet!!!                  --实体内容
```

l   请求方法URI协议/版本(请求行)
l   请求头(Request Header)
l   请求正文
下面是一个HTTP请求的例子：
GET/sample.jspHTTP/1.1
 
Accept:image/gif.image/jpeg,*/*
Accept-Language:zh-cn
Connection:Keep-Alive
Host:localhost
User-Agent:Mozila/4.0(compatible;MSIE5.01;Window NT5.0)
Accept-Encoding:gzip,deflate
 
username=jinqiao&password=1234

HTTP应答与HTTP请求相似，HTTP响应也由3个部分构成，分别是：
l 　状态行
l 　响应头(Response Header)
l 　响应正文
在接收和解释请求消息后，服务器会返回一个HTTP响应消息。
状态行由协议版本、数字形式的状态代码、及相应的状态描述，各元素之间以空格分隔。
格式:    HTTP-Version Status-Code Reason-Phrase CRLF
例如:    HTTP/1.1 200 OK \r\n

#### 浏览器Request Header和Response Header的内容

1)请求(客户端->服务端[request]) 
    GET(请求的方式) /newcoder/hello.html(请求的目标资源) HTTP/1.1(请求采用的协议和版本号) 
    Accept: */*(客户端能接收的资源类型) 
    Accept-Language: en-us(客户端接收的语言类型) 
    Connection: Keep-Alive(维护客户端和服务端的连接关系) 
    Host: localhost:8080(连接的目标主机和端口号) 
    Referer: http://localhost/links.asp(告诉服务器我来自于哪里) 
    User-Agent: Mozilla/4.0(客户端版本号的名字) 
    Accept-Encoding: gzip, deflate(客户端能接收的压缩数据的类型) 
    If-Modified-Since: Tue, 11 Jul 2000 18:23:51 GMT(缓存时间)  
    Cookie(客户端暂存服务端的信息) 
    Date: Tue, 11 Jul 2000 18:23:51 GMT(客户端请求服务端的时间)
2)响应(服务端->客户端[response])
    HTTP/1.1(响应采用的协议和版本号) 200(状态码) OK(描述信息)
    Location: http://www.baidu.com(服务端需要客户端访问的页面路径) 
    Server:apache tomcat(服务端的Web服务端名)
    Content-Encoding: gzip(服务端能够发送压缩编码类型) 
    Content-Length: 80(服务端发送的压缩数据的长度) 
    Content-Language: zh-cn(服务端发送的语言类型) 
    Content-Type: text/html; charset=GB2312(服务端发送的类型及采用的编码方式)
    Last-Modified: Tue, 11 Jul 2000 18:23:51 GMT(服务端对该资源最后修改的时间)
    Refresh: 1;url=http://www.it315.org(服务端要求客户端1秒钟后，刷新，然后访问指定的页面路径)
    Content-Disposition: attachment; filename=aaa.zip(服务端要求客户端以下载文件的方式打开该文件)
    Transfer-Encoding: chunked(分块传递数据到客户端）  
    Set-Cookie:SS=Q0=5Lb_nQ; path=/search(服务端发送到客户端的暂存数据)
    Expires: -1//3种(服务端禁止客户端缓存页面数据)
    Cache-Control: no-cache(服务端禁止客户端缓存页面数据)  
    Pragma: no-cache(服务端禁止客户端缓存页面数据)   
    Connection: close(1.0)/(1.1)Keep-Alive(维护客户端和服务端的连接关系)  

#### 状态码

<iframe width="100%" src="https://tool.oschina.net/commons?type=5"></iframe>
	
#### http协议缓存机制

Http 缓存机制作为 web 性能优化的重要手段，对于从事 Web 开发的同学们来说，应该是知识体系库中的一个基础环节，同时对于有志成为前端架构师的同学来说是必备的知识技能。
但是对于很多前端同学来说，仅仅只是知道浏览器会对请求的静态文件进行缓存，但是为什么被缓存，缓存是怎样生效的，却并不是很清楚。
在此，我会尝试用简单明了的文字，像大家系统的介绍HTTP缓存机制，期望对各位正确的理解前端缓存有所帮助。

##### 缓存规则

为方便大家理解，我们认为浏览器存在一个缓存数据库,用于存储缓存信息。
在客户端第一次请求数据时，此时缓存数据库中没有对应的缓存数据，需要请求服务器，服务器返回后，将数据存储至缓存数据库中

![c1](https://volc1612.gitee.io/blog/images/interview-question/c1.png)

HTTP缓存有多种规则，根据是否需要重新向服务器发起请求来分类，我将其分为两大类(<font color="red">强制缓存</font>，<font color="red">对比缓存</font>)
在详细介绍这两种规则之前，先通过时序图的方式，让大家对这两种规则有个简单了解。

已存在缓存数据时，仅基于强制缓存，请求数据的流程如下
![c2](https://volc1612.gitee.io/blog/images/interview-question/c2.png)
已存在缓存数据时，仅基于对比缓存，请求数据的流程如下
![c4](https://volc1612.gitee.io/blog/images/interview-question/c4.png)

我们可以看到两类缓存规则的不同，强制缓存如果生效，不需要再和服务器发生交互，而对比缓存不管是否生效，都需要与服务端发生交互。
两类缓存规则可以同时存在，强制缓存优先级高于对比缓存，也就是说，当执行强制缓存的规则时，如果缓存生效，直接使用缓存，不再执行对比缓存规则

##### 强制缓存

从上文我们得知，强制缓存，在缓存数据未失效的情况下，可以直接使用缓存数据，那么浏览器是如何判断缓存数据是否失效呢？
我们知道，在没有缓存数据的时候，浏览器向服务器请求数据时，<font color="red">服务器会将数据和缓存规则一并返回，缓存规则信息包含在响应header中</font>。
对于强制缓存来说，响应header中会有两个字段来标明失效规则（Expires/Cache-Control）
使用chrome的开发者工具，可以很明显的看到对于强制缓存生效时，网络请求的情况

###### Expires
Expires的值为服务端返回的到期时间，即下一次请求时，请求时间小于服务端返回的到期时间，直接使用缓存数据。
不过Expires 是HTTP 1.0的东西，现在默认浏览器均默认使用HTTP 1.1，所以它的作用基本忽略。
另一个问题是，到期时间是由服务端生成的，但是客户端时间可能跟服务端时间有误差，这就会导致缓存命中的误差。
所以HTTP 1.1 的版本，使用Cache-Control替代。

###### Cache-Control

Cache-Control 是最重要的规则。常见的取值有private、public、no-cache、max-age，no-store，默认为private。
private:             客户端可以缓存
public:              客户端和代理服务器都可缓存（前端的同学，可以认为public和private是一样的）
max-age=xxx:   缓存的内容将在 xxx 秒后失效
no-cache:          需要使用对比缓存来验证缓存数据（后面介绍）
no-store:           所有内容都不会缓存，强制缓存，对比缓存都不会触发（对于前端开发来说，缓存越多越好，so...基本上和它说886）
举个板栗
![c6](https://volc1612.gitee.io/blog/images/interview-question/c6.png)
图中Cache-Control仅指定了max-age，所以默认为private，缓存时间为31536000秒（365天）
也就是说，在365天内再次请求这条数据，都会直接获取缓存数据库中的数据，直接使用。

##### 对比缓存

对比缓存，顾名思义，需要进行比较判断是否可以使用缓存。
浏览器第一次请求数据时，服务器会将缓存标识与数据一起返回给客户端，客户端将二者备份至缓存数据库中。
再次请求数据时，客户端将备份的缓存标识发送给服务器，服务器根据缓存标识进行判断，判断成功后，返回304状态码，通知客户端比较成功，可以使用缓存数据。

第一次访问：
![c7](https://volc1612.gitee.io/blog/images/interview-question/c7.png)

第二次访问：
![c8](https://volc1612.gitee.io/blog/images/interview-question/c8.png)

通过两图的对比，我们可以很清楚的发现，在对比缓存生效时，状态码为304，并且报文大小和请求时间大大减少。
原因是，服务端在进行标识比较后，只返回header部分，通过状态码通知客户端使用缓存，不再需要将报文主体部分返回给客户端。
对于对比缓存来说，缓存标识的传递是我们着重需要理解的，它在请求header和响应header间进行传递，
一共分为两种标识传递，接下来，我们分开介绍。

###### Last-Modified/If-Modified-Since

Last-Modified：
服务器在响应请求时，告诉浏览器资源的最后修改时间。
![c9](https://volc1612.gitee.io/blog/images/interview-question/c9.png)

If-Modified-Since：
再次请求服务器时，通过此字段通知服务器上次请求时，服务器返回的资源最后修改时间。
服务器收到请求后发现有头If-Modified-Since 则与被请求资源的最后修改时间进行比对。
若资源的最后修改时间大于If-Modified-Since，说明资源又被改动过，则响应整片资源内容，返回状态码200；
若资源的最后修改时间小于或等于If-Modified-Since，说明资源无新修改，则响应HTTP 304，告知浏览器继续使用所保存的cache。
![c10](https://volc1612.gitee.io/blog/images/interview-question/c10.png)

###### Etag/If-None-Match
优先级高于Last-Modified  /  If-Modified-Since
Etag：
服务器响应请求时，告诉浏览器当前资源在服务器的唯一标识（生成规则由服务器决定）。
![c11](https://volc1612.gitee.io/blog/images/interview-question/c11.png)

If-None-Match：
再次请求服务器时，通过此字段通知服务器客户段缓存数据的唯一标识。
服务器收到请求后发现有头If-None-Match 则与被请求资源的唯一标识进行比对，
不同，说明资源又被改动过，则响应整片资源内容，返回状态码200；
相同，说明资源无新修改，则响应HTTP 304，告知浏览器继续使用所保存的cache。
![c12](https://volc1612.gitee.io/blog/images/interview-question/c12.png)
总结
对于强制缓存，服务器通知浏览器一个缓存时间，在缓存时间内，下次请求，直接用缓存，不在时间内，执行比较缓存策略。
对于比较缓存，将缓存信息中的Etag和Last-Modified通过请求发送给服务器，由服务器校验，返回304状态码时，浏览器直接使用缓存。

浏览器第一次请求：
![c13](https://volc1612.gitee.io/blog/images/interview-question/c13.png)

浏览器再次请求时：
![c14](https://volc1612.gitee.io/blog/images/interview-question/c14.png)

### GET和POST的区别

（1）get浏览器会保存历史记录缓存，post不保存
（1）   在客户端，Get方式在通过URL提交数据，数据在URL中可以看到；POST方式，数据放置在HTML HEADER内提交。
（2） 对于get方式，服务器端用Request.QueryString获取变量的值，对于post方式，服务器端用Request.Form获取提交的数据。
（2）   GET方式提交的数据最多只能有1024字节(1K)，而POST则没有此限制。
（3）   安全性问题。正如在（1）中提到，使用 Get 的时候，参数会显示在地址栏上，而 Post 不会。所以，如果这些数据是中文数据而且是非敏感数据，那么使用 get；如果用户输入的数据不是中文字符而且包含敏感数据，那么还是使用 post为好。
（4） 如果GET请求的表单action属性中已经包含参数，浏览器会直接将其过滤掉，再附加form表单数据

### TCP和UDP的异同

TCP和UDP：传输层协议；
HTTP：应用层协议；
SOCKET：TCP/IP网络的API；

传输协议
通讯的规则
常见协议：TCP，UDP
在socket中使用tcp和udp
我的总结：
一、UDP:
                1、将数据源和目的地封装到数据包中，不需要建立连接
                2、每个数据包的大小限制在64k以内
                3、因无连接，是不可靠协议
                4、不需要建立连接，速度快

例子：聊天、对讲机就是UDP的，面向无连接（不管在不在，知不知道，只管发送，求速度），丢数据也不管。速度快。数据被分成包



二、TCP:
                1、建立连接，形成传输数据的通道
                2、在连接中进行大量数据的传输
                3、通过三次握手完成连接、是可靠协议
                4、必须建立连接，效率会稍低

例子：电话通话，必须连接，对方同意才可以发送数据（不然就等待），不能丢失数据。

### 单例模式(饿汉与懒汉)

单利模式满足两个条件：
- 生成类的实例要唯一。具有私有构造方法
- 生成实例的方法必须是全局静态方法。原因是非静态方法必须通过实例进行调用，如果已经有了实例，我们还需要生成实例的方法干什么呢？

```java
/**
 * 饿汉式单利模式
 * @author Administrator
 *
 */
public class Single {
	private static Single instance = new Single();
	private Single() {
	}
	public static Single getInstance(){
		return instance;
	}
	
	public void method2(){
		System.out.println("Single的mehtod2方法");
	}
	public void method3(){
		System.out.println("Single的method3方法");
	}
}
```

```java
/**
 * lazy单例模式
 */
class SingleLazy{
		
	private static SingleLazy instance = null;

	private SingleLazy() {
	}
	
	public static synchronized SingleLazy getInstance(){
		if(instance == null){
			return instance = new SingleLazy();
		}else{
			return instance;
		}
	}
}
```

### 简述Socket

Socket
Socket就是为网络服务提供的一种机制。
通信的两端都有Socket。
网络通信其实就是Socket间的通信。
数据在两个Socket间通过IO传输。
SOCKET：TCP/IP网络的API
下面是示例程序的简单步骤说明
服务器端：
第一步：用指定的端口号和服务器的ip建立一个EndPoint对像；
第二步：建立一个Socket对像；
第三步：用socket对像的Bind()方法绑定EndPoint；
第四步：用socket对像的Listen()方法开始监听；
第五步：接受到客户端的连接，用socket对像的Accept()方法创建新的socket对像用于和请求的客户端进行通信;
第六步：通信结束后一定记得关闭socket;

```java
using System;
using System.Collections.Generic;
using System.Text;
using System.Net;
using System.Net.Sockets;

namespace server
{
    class Program
     {
        static void Main(string[] args)
         {
            int port = 2000;
            string host = "127.0.0.1";


            /**////创建终结点（EndPoint）
             IPAddress ip  = IPAddress.Parse(host);//把ip地址字符串转换为IPAddress类型的实例
             IPEndPoint ipe = new IPEndPoint(ip, port);//用指定的端口和ip初始化IPEndPoint类的新实例


            /**////创建socket并开始监听
             Socket s　=　new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);//创建一个socket对像，如果用udp协议，则要用SocketType.Dgram类型的套接字
             s.Bind(ipe);//绑定EndPoint对像（2000端口和ip地址）
             s.Listen(0);//开始监听
             Console.WriteLine("等待客户端连接");


            /**////接受到client连接，为此连接建立新的socket，并接受信息
             Socket temp = s.Accept();//为新建连接创建新的socket
             Console.WriteLine("建立连接");
            string recvStr = "";
            byte[] recvBytes = new byte[1024];
            int bytes;
             bytes = temp.Receive(recvBytes, recvBytes.Length, 0);//从客户端接受信息
             recvStr += Encoding.ASCII.GetString(recvBytes, 0, bytes);


            /**////给client端返回信息
             Console.WriteLine("server get message:{0}", recvStr);//把客户端传来的信息显示出来
            string sendStr = "ok!Client send message successful!";
            byte[] bs = Encoding.ASCII.GetBytes(sendStr);
             temp.Send(bs, bs.Length, 0);//返回信息给客户端
             temp.Close();
             s.Close();
             Console.ReadLine();
         }
       
     }
}
```

客户端：
第一步：用指定的端口号和服务器的ip建立一个EndPoint对像；
第二步：建立一个Socket对像；
第三步：用socket对像的Connect()方法以上面建立的EndPoint对像做为参数，向服务器发出连接请求；
第四步：如果连接成功，就用socket对像的Send()方法向服务器发送信息；
第五步：用socket对像的Receive()方法接受服务器发来的信息 ;
//获取服务端回传的数据
		InputStream is = sc.getInputStream();
第六步：通信结束后一定记得关闭socket；

```java
using System;
using System.Collections.Generic;
using System.Text;
using System.Net;
using System.Net.Sockets;

namespace Client
{
    class Program
     {
        static void Main(string[] args)
         {
            try
             {
                int port = 2000;
                string host = "127.0.0.1";

                /**////创建终结点EndPoint
                 IPAddress ip = IPAddress.Parse(host);
                //IPAddress ipp = new IPAddress("127.0.0.1");
                 IPEndPoint ipe = new IPEndPoint(ip, port);//把ip和端口转化为IPEndpoint实例


                /**////创建socket并连接到服务器
                 Socket c = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);//创建Socket
                 Console.WriteLine("Conneting…");
                 c.Connect(ipe);//连接到服务器


                /**////向服务器发送信息
                string sendStr = "hello!This is a socket test";
                byte[] bs = Encoding.ASCII.GetBytes(sendStr);//把字符串编码为字节
                 Console.WriteLine("Send Message");
                 c.Send(bs, bs.Length, 0);//发送信息


                /**////接受从服务器返回的信息
                string recvStr = "";
                byte[] recvBytes = new byte[1024];
                int bytes;
                 bytes = c.Receive(recvBytes, recvBytes.Length, 0);//从服务器端接受返回信息
                 recvStr += Encoding.ASCII.GetString(recvBytes, 0, bytes);
                 Console.WriteLine("client get message:{0}", recvStr);//显示服务器返回信息

                /**////一定记着用完socket后要关闭
                 c.Close();

             }
            catch (ArgumentNullException e)
             {
                 Console.WriteLine("argumentNullException: {0}", e);
             }
            catch (SocketException e)
             {
                 Console.WriteLine("SocketException:{0}", e);
             }

             Console.WriteLine("Press Enter to Exit");
         }
     }
}
```

### TCP 服务器的工作流程

1.	服务器调用 socket() 创建 socket;
2.	服务器调用 bind() 绑定端口和 ip;
3.	服务器调用 listen() 设置缓冲区;
4.	服务器调用 accept() 接收客户端的连接请求建立连接, 生成一个新的 socket;
5.	服务器与客户端建立好连接之后, 就可以通过 send()/recv() 向客户端发送或者接收客户端的数据;
6.	服务器调用 close() 关闭 socket.
客户端工作流程:
7.	客户端调用 socket() 创建 socket;
8.	客户端调用 connect() 向服务器发去连接请求以建立连接;
9.	客户端与服务器建立连接之后, 就可以通过 send()/recv() 向服务器发送或者接收服务器的数据;
10.	客户端调用 clos() 关闭 socket.


TCP 服务器的工作流程:
1.	服务器调用 socket() 创建 socket;
2.	服务器调用 bind() 绑定端口和 ip;
3.	服务器调用 listen() 设置缓冲区;
4.	服务器调用 accept() 接收客户端的连接请求建立连接, 生成一个新的 socket;
5.	服务器与客户端建立好连接之后, 就可以通过 send()/recv() 向客户端发送或者接收客户端的数据;
6.	服务器调用 close() 关闭 socket.
客户端工作流程:
7.	客户端调用 socket() 创建 socket;
8.	客户端调用 connect() 向服务器发去连接请求以建立连接;
9.	客户端与服务器建立连接之后, 就可以通过 send()/recv() 向服务器发送或者接收服务器的数据;
10.	客户端调用 clos() 关闭 socket



### jdbc工作流程

什么是JDBC
JDBC的全称是Java DataBase Connection，也就是Java数据库连接，我们可以用它来操作关系型数据库。JDBC接口及相关类在java.sql包和javax.sql包里。我们可以用它来连接数据库，执行SQL查询，存储过程，并处理返回的结果。
JDBC接口让Java程序和JDBC驱动实现了松耦合，使得切换不同的数据库变得更加简单

#### 工作原理

1）获取数据库管理驱动。DriverManage  dm = Class.forName("com.mysql.jdbc.Driver");
2) 获取connection对象。Connection conn = dm.getConnection(String url, String user, String passWord);
3) 获取statement,createStatement();或者创建prepareStatement(sql)预编译对象。
4）执行
	execute();任何sql语句
	executeUpdate();执行DML;DDL语句
	executeQuery();返回ResualtSet结果集对象。
5）取出结果集对象中的数据，next(),first(),last()移动指针指向记录数。再使用getXxx()获取列数据
6）回收数据库资源，从后向前依次关闭ResualtSet，statement，connection对象。

#### Statement、PreperStateent、CallableStatement之间的区别

Statement(接口)
     |
PreparedStatement(接口)
     |
CallableStatement(接口)
以上三者为继承关系。
Statement:(用于执行不带参数的简单 SQL 语句)
    每次执行sql语句，数据库都要执行sql语句的编译，最好用于仅执行一次查询并返回结果的情形，效率高于PreparedStatement.
```java
String sql = "select seq_orderdetid.nextval as orderidfrom dual";
    Statement stat1=conn.createStatement();
    ResultSet rs1 = stat1.executeQuery(sql);
    if ( rs1.next() ) {
     id = rs1.getLong(1);
    }
```
PreparedStatement:(用于执行带或不带 IN 参数的预编译SQL 语句)
    执行的SQL语句中是可以带参数的,并支持批量执行SQL。由于采用Cache机制，则预先编译的语句，就会放在Cache中，下次执行相同SQL语句时，则可以直接从Cache中取出来。
```java
  方法一：
    PreparedStatement pstmt  = con.prepareStatement("UPDATE EMPLOYEES  SET SALARY = ? WHERE ID =?");
    pstmt.setBigDecimal(1, 153833.00);
    pstmt.setInt(2, 110592);
pstmt. executeUpdate();
  方法二：
PreparedStatement pstmt  = con.prepareStatement("UPDATE EMPLOYEES  SET SALARY = ? WHERE ID =?");
   for(int i =0;i<length;i++){
        pstmt.setBigDecimal(1, param1[i]);
        pstmt.setInt(2, param2[i]);
        pstmt.addBatch();
    }
    pstmt. executeBatch();
```

CallableStatement :(用于执行对数据库已存储过程的调用)
作为示例，下述代码先注册 OUT 参数，执行由 cstmt 所调用的已储存过程，然后检索在 OUT 参数中返回的值。方法 getByte 从第一个 OUT 参数中取出一个 Java 字节，而 getBigDecimal 从第二个 OUT 参数中取出一个 BigDecimal 对象（小数点后面带三位数）： 

CallableStatement cstmt = con.prepareCall( 
"{call getTestData(?, ?)}"); 
cstmt.registerOutParameter(1, java.sql.Types.TINYINT); 
cstmt.registerOutParameter(2, java.sql.Types.DECIMAL, 3); 
cstmt.executeQuery(); 
byte x = cstmt.getByte(1); 
java.math.BigDecimal n = cstmt.getBigDecimal(2, 3); 

CallableStatement 与 ResultSet 不同，它不提供用增量方式检索大 OUT 值的特殊机制。

#### 注册驱动
//Class.forName("com.mysql.jdbc.Driver");
			//首先要导包,Class.forName(driver)的根本目的就是为了调用DriverManager.registerDriver。注册驱动
			//jdbc4已经不需要显式的调用Class.forName了，在jdbc4中，调用getConnection的时候DriverManager会自动去加载合适的驱动

### 接口Statement中定义的execute方法的返回类型是? 代表的含义? executeQuery方法返回的类型是?  executeUpdate返回的类型是? 代表的含义是?

exectue
```java
@Test
	public void testExecute() throws Exception{
		Connection connection = JDBCUtils.getConnection();
		String sql1 = "select * from admin";
		Statement createStatement = connection.createStatement();
		boolean execute = createStatement.execute(sql1);//表示是否成功返回了ResultSet对象
		System.out.println(execute);
		ResultSet resultSet = createStatement.getResultSet();
		while(resultSet.next()){
			System.out.println(resultSet.getString(1));
		}
	}
```
exetueQuery
```java
@Test
	public void testExecuteQuery() throws Exception{
		Connection connection = JDBCUtils.getConnection();
		String sql = "select * from admin";
		Statement createStatement = connection.createStatement();
		ResultSet executeQuery = createStatement.executeQuery(sql);
		while(executeQuery.next()){
			System.out.println(executeQuery.getString(1));
		}
	}
```
executeUpdata
```java
	@Test
	public void testExecuteUpdata() throws Exception{
		Connection connection = JDBCUtils.getConnection();
		String sql = "INSERT INTO admin(id,userName) VALUES(4,'李四');";
		Statement createStatement = connection.createStatement();
		int data = createStatement.executeUpdate(sql);//影响几条数据
		System.out.println(data);
	}
```

### servlet工作流程

构造方法： 创建servlet对象的时候调用。tomcat创建servletConfig和servletContext接口的实现类对象standardWrapper和applicationContext。默认情况下，第一次访问servlet的时候创建servlet对象只调用1次。证明servlet对象在tomcat是单实例的。
init方法： 创建完servlet对象的时候调用。只调用1次。在service方法调用之前会创建request和response
service方法： 每次发出请求时调用。调用n次。
destroy方法： 销毁servlet对象的时候调用。停止服务器或者重新部署web应用时销毁servlet对象。只调用1次。

1）通过映射找到到servlet-class的内容，字符串： gz.itcast.a_servlet.FirstServlet
2）通过反射构造FirstServlet对象
	2.1 得到字节码对象
		Class clazz = class.forName("gz.itcast.a_servlet.FirstServlet");
	2.2 调用无参数的构造方法来构造对象
		Object obj = clazz.newInstance();     ---1.servlet的构造方法被调用
3）创建ServletConfig对象，通过反射调用init方法
	3.1 得到方法对象
		Method m = clazz.getDeclareMethod("init",ServletConfig.class);//方法名和方法接收的对象类型(创建ServletConfig的class对象)
	3.2 调用方法
		m.invoke(obj,config);            --2.servlet的init方法被调用
4）创建request，response对象，通过反射调用service方法
	4.1 得到方法对象
		Methodm m =clazz.getDeclareMethod("service",HttpServletRequest.class,HttpServletResponse.class);
	4.2 调用方法
		m.invoke(obj,request,response);  --3.servlet的service方法被调用
5）当tomcat服务器停止或web应用重新部署，通过反射调用destroy方法
	5.1 得到方法对象
		Method m = clazz.getDeclareMethod("destroy",null);
	5.2 调用方法
		m.invoke(obj,null);          --4.servlet的destroy方法被调用


### 重定向和转发

重定向
```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * 重定向
 */
@Controller
@RequestMapping("/redirectTest")
public class RedirectController {
    @RequestMapping("redirectTest")
    public String testForward(HttpServletRequest request, HttpServletResponse response) throws IOException {
        // 服务内部资源
//         String path = "/viewTest?name=1";
        // 服务外部资源
        String path = "http://www.baidu.com";
        response.sendRedirect(path); // sendRedirect方法无返回值，转发到http://www.baidu.com资源并返回页面
        System.out.println("执行完重定向后"); // 继续执行代码
        return "testJs"; // 返回testJs页面
    }
}


```

转发
```java
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * 转发
 */
@RestController
@RequestMapping("/forwardTest")
public class ForwardController {

    @RequestMapping("forwardTest")
    public String testredirect(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 转发
        RequestDispatcher rd = request.getRequestDispatcher("/hello");
        rd.forward(request, response); // forward方法无返回值，重定向到hello资源并返回hello方法处理结果
        System.out.println("执行完转发后"); // 继续执行代码
        return "testJs"; // 不返回testJs页面
    }
}
```

重定向
sendRedirect("index.jsp");
转发
forward(request, response);

重定向和转发都返回void值

区别：
- 转发的速度快；重定向速度慢
- 转发的是同一次请求；重定向是两次不同请求
- 转发地址栏没有变化；重定向地址栏有变化
- 转发必须是在同一台服务器下完成；重定向可以在不同的服务器下完成


### Struts2的工作流程

![q](https://volc1612.gitee.io/blog/images/interview-question/q.jpg)

1 客户端初始化一个指向Servlet容器（例如Tomcat）的请求；
2 这个请求经过一系列的过滤器，struts核心过滤器StrutsPrepareAndExcuteFilter；
3 接着FilterDispatcher被调用，它是服务于controller(控制层)，FilterDispatcher询问ActionMapper来决定这个请是否需要调用某个Action，需要调用 ActionMapper返回一个收集Action详细信息的ActionMaping对象，给filterdispatcer；
4 FilterDispatcher把请求的处理交给ActionProxy；
5 FilterDispatcher将控制权委派给ActionProxy,ActionProxy调用配置管理器Configuration Manager（struts.xml），找到需要调用的Action类。例如上一篇的StrutsAction类就是Action类；
6接着ActionProxy创建一个ActionInvocation对象，ActionInvocation在调用Action之前会依次根据配置加载Action相关的所有拦截器（Interceptor ）；
7 一旦Action执行完毕，ActionInvocation负责根据struts.xml中的配置找到对应的返回结果；

优点：
	1.struts是基于MVC模式的。应用程序进行了明确的分层，使程序员更关注于业务逻辑的实现。
	2.struts有着丰富的taglib,则能大大提高开发效率
缺点：
1.需要配置forward的视图资源的转发和重定向。每一个action对象一个result。而且配置完成之后还要重新启动服务器tomcat
2.测试不方便


	每一个action都和web层耦合在一起，测试依赖于web容器，单元测试很难实现。
3.Struts 的Action必需是thread－safe方式，它仅仅允许一个实例去处理所有的请求。所以action用到的所有的资源都必需统一同步，这个就引起了线程安全的问题


4. 对事件支持不够. 在struts中，实际是一个表单Form对应一个Action类(或DispatchAction)，换一句话说：在Struts中实际是一个表单只能对应一个事件，struts这种事件方式称为application event，application event和component event相比是一种粗粒度的事件。


lock

### Struts常用标签
```
<s:textfield name="info.title" cssClass="s_text" id="infoTitle"  cssStyle="width:160px;"/>
```

if else
```
<s:if test="%{#session.SYS_USER.headImg != null && #session.SYS_USER.headImg !=''}">
                            	<img src="${ctx}/upload/<s:property value='#session.SYS_USER.headImg'/>" width="70" height="70" />
                            </s:if><s:else>
								<img src="${ctx}/images/home/gs09.png" width="70" height="70" />
							</s:else>
```
url a
```
<s:url var="infoViewUrl" action="home_infoViewUI" namespace="/sys" >
                    	<s:param name="info.infoId"><s:property value="infoId"/></s:param>
                    </s:url>
                    <s:a href="%{#infoViewUrl}" target="_blank">
                    	<s:property value="title"/>
                   	</s:a>
```
iteroter
```
<s:iterator value="#infoList">
                <tr>
                    <td height="23">
                    <s:url var="infoViewUrl" action="home_infoViewUI" namespace="/sys" >
                    	<s:param name="info.infoId"><s:property value="infoId"/></s:param>
                    </s:url>
                    <s:a href="%{#infoViewUrl}" target="_blank">
                    	<s:property value="title"/>
                   	</s:a>
                    </td>
                    <td width="150px"><s:property value="#infoTypeMap[type]"/></td>
                    <td width="150px"><s:property value="creator"/></td>
                    <td width="150px"><s:date name="createTime" format="yyyy-MM-dd HH:mm"/></td>
                </tr>
                </s:iterator>
```
checkboxlist
```
<td class="tdBg" width="200px">角色：</td>
            <td>
            	<s:checkboxlist list="#roleList" name="userRoleIds" listKey="roleId" listValue="name"></s:checkboxlist>
            </td>
```

### struts.xml样式

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
          "-//Apache Software Foundation//DTD Struts Configuration 2.0//EN"
          "http://struts.apache.org/dtds/struts-2.0.dtd">
<struts>
    <package name="firstStruts" extends="struts-default">
    	<action name="helloStruts" class="cn.volc.struts.FirstStruts" method="execute">
    		<result name="success">/jsp/success.jsp</result>
    	</action>
    </package>  
</struts>
```
### web.xml配置过滤器
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">
  <display-name>HelloStruts</display-name>
  <filter>
  	<filter-name>struts2</filter-name>
  	<filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
  </filter>
  <filter-mapping>
  	<filter-name>struts2</filter-name>
  	<url-pattern>/*</url-pattern>
  </filter-mapping>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
</web-app>
```
### El表达式语言

	用于页面取值，jsp页面取值的标准。(默认直接可以使用) (应用范围更广。)
### Ognl表达式语言
	 struts标签默认支持的表达式语言。必须配置struts标签用，不能离开struts标签直接用。

### 拦截器功能与过滤器功能类似
	区别：
		共同点： 都拦截资源！
		区别：
			过滤器，拦截器所有资源都可以；  (/index.jsp/servlet/img/css/js)
			拦截器，只拦截action请求。
				在strtus.xml文件中配置指定的拦截器。在拦截器类中可获取hibernate的session对象。再开始事物session.beginTransaction();
				再放行String result = invocation.invoke();去执行对应的action后再提交session.getTransaction().commit();
			拦截器是struts的概念，只能在struts中用。
			过滤器是servlet的概念，可以在struts项目、servlet项目用。
// 面试题： 拦截器什么时候执行？ (访问/启动)  先执行action类创建，先执行拦截器？
	// --》 1. 用户访问时候按顺序执行18个拦截器；
	//---》 2. 先执行Action类的创建，再执行拦截器； 最后拦截器执行完，再执行业务方法

### struct2的Interceptor有什么用？基于什么技术

拦截器的设计，就是基于组件设计的应用！
拦截器是基于java的反射机制的，而过滤器是基于函数回调 。 
2、过滤器依赖与servlet容器，而拦截器不依赖与servlet容器 。 
3、拦截器只能对action请求起作用，而过滤器则可以对几乎所有的请求 起作用 。 
4、拦截器可以访问action上下文、值栈里的对象，而过滤器不能 。 
5、在action的生命周期中，拦截器可以多次被调用，而过滤器只能在容器初始化时被调用一次 

项目中用户权限的判定

### interceptor执行流程

Struts2拦截器的实现原理相对简单，当请求struts2的action时，Struts 2会查找配置文件，并根据其配置实例化相对的拦截器对象，然后串成一个列表，最后一个一个地调用列表中的拦截器

![r](https://volc1612.gitee.io/blog/images/interview-question/r.png)
 
struts-default.xml文件中，定义了struts提供的所有拦截器！
```
//1. 定义拦截器以及拦截器栈
<interceptors>
    1.1 拦截器定义
    <interceptor name="" class="" /> 
    
    1.2 拦截器栈的定义
    <interceptor-stack name="defaultStack">
	引用了上面拦截器(1.1)
    </interceptor-stack>
</interceptors>

2. 默认执行的拦截器(栈)
<default-interceptor-ref name="defaultStack"/>
```

### 数据库

#### unicode

是一种编码方式，和ascii是同一个概念，而UTF是一种存储方式（格式）。
数据存储是以“字节”(Byte)为单位,数据传输是以“位”（bit）为单位，一个位就代表一个0或1（即二进制），每8个位（bit）组成一个字节（Byte）。1KB=1024B，1KB=1024Byte=1024*8bit
8位二进制数(表示一个字节)
1000  0000(-128)  -  1111  1111(-1)[8位二进制范围为255，用于ip地址：255.255.255.255]
字符与字节
ASCII码：一个英文字母（不分大小写）占一个字节的空间，一个中文汉字占两个字节的空间。
UTF-8编码：一个英文字符等于一个字节，一个中文（含繁体）等于二~四个字节。
Unicode编码：一个英文等于两个字节，一个中文（含繁体）等于两个字节。
符号：英文标点占一个字节，中文标点占两个字节。举例：英文句号“.”占1个字节的大小，中文句号“。”占2个字节的大小。


#### sql语句

DDL:数据定义语言INSERT /UPDATE/DELETE/
DML:数据操作语言CREATE/ALTER/DROP/TRUNCATE
DCL:数据控制语言grant/revoke

CREATE TABLE t_stu(
	id INT(4) PRIMARY KEY,
	userName VARCHAR(20),
	gender VARCHAR(4) DEFAULT '男'
)
DROP TABLE t_stu;
INSERT INTO t_stu(userName) VALUES('张三');
INSERT INTO t_stu(gender,id,userName) VALUE(,2,'张三3'); -- 会报错

#### mysql分页

-- 起始行从0开始
-- 分页：当前页  每页显示多少条
-- 分页查询当前页的数据的sql: SELECT * FROM student LIMIT (当前页-1)*每页显示多少条,每页显示多少条;
-- 需求： 查询第1,2条记录（第1页的数据）
SELECT * FROM student LIMIT 0,2;
-- 查询第3,4条记录（第2页的数据）

SELECT * FROM student LIMIT 2,2;
oracle分页

显示emp表中3-8条记录（方式一：使用集合减运算）
select rownum "伪列",emp.* from emp where rownum<=8
minus
select rownum,emp.* from emp where rownum<=2;

显示emp表中3-8条记录（方式二：使用子查询，在from子句中使用，重点）
select xx.*
from (select rownum ids,emp.* from emp where rownum<=8) xx 
where ids>=2;
注意：在子查询中的别名，不可加""引号


### 函数与存储过程使用场景

函数:
创建
```
create or replace function findEmpIncome(pempno in number) return number
as
	yearSal number;
begin
	select sal*12+NVL(comm,0) into yearSal from emp where empno=pempno;
	return yearSal;
end;
/
```
调用
```
declare
	yearSal number;
begin
	yearSal := findEmpIncome(7369);
	dbms_output.put_line('7369号员工的年薪是' || yearSal);
end;
/
```
过程：
创建
向emp表中插入数据
```
CREATE or replase procedure batchInsert
as
	i number(4) := 1; -- 循环插入999条数据，赋值：=
begin
	for i in 1...999
	loop
		insert into emp(empno,ename) values(I,”test”)
	end loop
end;
/	
```
调用
```
exec batchInsert;
```
【适合使用】过程函数：
	    》需要长期保存在数据库中
            》需要被多个用户重复调用
            》业务逻辑相同，只是参数不一样
	    》批操作大量数据，例如：批量插入很多数据


函数：有且之有一个返回值时用函数
	过程：没有或有多个返回值时
过程函数使用场景：
	需要长期保存的数据
	需要被多个用户重复调用
	业务逻辑相同只是参数不一样
	批操作大量数据，例如：破凉插入跟多数据
SQL使用场景：
	分时上述反面，都可使用SQL
	对表，视图，序列，索引，等这些嗨哟要用SQL

### 关于Oracle事务的总结
#### 什么是事务，事务的特性是什么

事务的任务便是使数据库从一种状态变换成为另一种状态，这不同于文件系统，它是数据库所特用的。它的特性有四个：TOM总结为ACID即
原子性atomicity:语句级原子性，过程级原子性，事务级原子性
一致性consistency:状态一致，同一事务中不会有两种状态
隔离性isolation:事务间是互相分离的互不影响(这里可能也有自治事务)
持久性durability:事务提交了，那么状态就是永久的

回顾MySQL的事务开始：start transaction
Oracle的事务开始：第一条DML操作做为事务开始

Oracle的提交事务
（1）显示提交：commit	
（2）隐藏提交：DDL/DCL/exit(sqlplus工具)
注意：提交是的从事务开始到事务提交中间的内容，提交到ORCL数据库中的DBF二进制文件
使用savepoint 回滚点，设置回滚点a	
savepoint a;
使用rollback to savepoint，回滚到回滚点a处
rollback to savepoint a;

#### PLSQL
     是专用于Oracle服务器，在SQL基础之上，添加了一些过程化控制语句，叫PLSQL
     过程化包括有：类型定义，判断，循环，游标，异常或例外处理。。。
     PLSQL强调过程
为什么要用PLSQL
     因为SQL是第四代命令式语言，无法显示处理过程化的业务，所以得用一个过程化程序设计语言来弥补SQL的不足之处，
     SQL和PLSQL不是替代关系，是弥补关系	
PLSQL程序的完整组成结构如下：
     [declare]
          变量声明;
  	  变量声明;
     begin
          DML/TCL操作;
	  DML/TCL操作;
     [exception]
          例外处理;
	  例外处理;
     end;
     /
注意：在PLSQL程序中，；号表示每条语句的结束，/表示整个PLSQL程序结束
### 触发器作用
需求定时插入：
create or replace trigger securityTigger
before
insert
on emp
declare
	pday varchar2(20);
	phour number(2);
begin
	select to_char(sysdate,'day') into pday from dual;
	select to_char(sysdate,'hh24') into phour from dual;
	if pday in('星期六','星期日') or phour not between 7 and 23 then
		raise_application_error('-2000','非工作时间不能向emp表中插入数据');
	end if;
end;
/

当操作了某张表时，希望同时触发一些动作/行为，可以使用触发器完成！！
例如： 当向员工表插入一条记录时，希望同时往日志表插入数据

### oracle sql数据库的优化
01）选择最有效率的表名顺序（笔试常考） 
      ORACLE的解析器按照从右到左的顺序处理FROM子句中的表名，
      FROM子句中写在最后的表将被最先处理，
      在FROM子句中包含多个表的情况下,你必须选择记录条数最少的表放在最后，
      如果有3个以上的表连接查询,那就需要选择那个被其他表所引用的表放在最后。
      例如：查询员工的编号，姓名，工资，工资等级，部门名
      select emp.empno,emp.ename,emp.sal,salgrade.grade,dept.dname
      from salgrade,dept,emp
      where (emp.deptno = dept.deptno) and (emp.sal between salgrade.losal and salgrade.hisal)  		
      1)如果三个表是完全无关系的话，将记录和列名最少的表，写在最后，然后依次类推
      2)如果三个表是有关系的话，将引用最多的表，放在最后，然后依次类推


（02）WHERE子句中的连接顺序（笔试常考）  
      ORACLE采用自右而左的顺序解析WHERE子句,根据这个原理,表之间的连接必须写在其他WHERE条件之左,
      那些可以过滤掉最大数量记录的条件必须写在WHERE子句的之右。  
      例如：查询员工的编号，姓名，工资，部门名  
      select emp.empno,emp.ename,emp.sal,dept.dname
      from emp,dept
      where (emp.deptno = dept.deptno) and (emp.sal > 1500)
  	
（03）SELECT子句中避免使用*号
      ORACLE在解析的过程中,会将*依次转换成所有的列名，这个工作是通过查询数据字典完成的，这意味着将耗费更多的时间
      select empno,ename from emp;

（04）使用DECODE函数来减少处理时间
      使用DECODE函数可以避免重复扫描相同记录或重复连接相同的表

（05）整合简单，无关联的数据库访问

（06）用TRUNCATE替代DELETE
   
（07）尽量多使用COMMIT
      因为COMMIT会释放回滚点

（08）用WHERE子句替换HAVING子句
      WHERE先执行，HAVING后执行
     
（09）多使用内部函数提高SQL效率
     
（10）使用表的别名
      salgrade s
     
（11）使用列的别名
      ename e

（12）用索引提高效率
      在查询中，善用索引
      
（13）字符串型，能用=号，不用like
      因为=号表示精确比较，like表示模糊比较 

（14）SQL语句用大写的
      因为Oracle服务器总是先将小写字母转成大写后，才执行
      在eclipse中，先写小写字母，再通过ctrl+shift+X转大写;ctrl+shift+Y转小写	

（15）避免在索引列上使用NOT
      因为Oracle服务器遇到NOT后，他就会停止目前的工作，转而执行全表扫描

（16）避免在索引列上使用计算
      WHERE子句中，如果索引列是函数的一部分，优化器将不使用索引而使用全表扫描，这样会变得变慢 
      例如，SAL列上有索引，
      低效：
      SELECT EMPNO,ENAME
      FROM EMP 
      WHERE SAL*12 > 24000;
      高效：
      SELECT EMPNO,ENAME
      FROM EMP
      WHERE SAL > 24000/12;

（17）用 >= 替代 >
      低效：
      SELECT * FROM EMP WHERE DEPTNO > 3   
      首先定位到DEPTNO=3的记录并且扫描到第一个DEPT大于3的记录
      高效：
      SELECT * FROM EMP WHERE DEPTNO >= 4  
      直接跳到第一个DEPT等于4的记录

（18）用IN替代OR
      select * from emp where sal = 1500 or sal = 3000 or sal = 800;
      select * from emp where sal in (1500,3000,800);

（19）总是使用索引的第一个列
      如果索引是建立在多个列上，只有在它的第一个列被WHERE子句引用时，优化器才会选择使用该索引
      当只引用索引的第二个列时，不引用索引的第一个列时，优化器使用了全表扫描而忽略了索引
      create index emp_sal_job_idex
      on emp(sal,job);
      ----------------------------------
      select *
      from emp  
      where job != 'SALES';	      

（20）避免改变索引列的类型，显示比隐式更安全 
      当字符和数值比较时，ORACLE会优先转换数值类型到字符类型 
      select 123 || '123' from dual;

### oracle面试题

--------------------------------------------------------------------------------

01）oracle10i，oracle11g，oracle12c，其它i,g,c什么意思？
	internet
	grit(网格运算)，多个服务器相连
	cloud云计算
02）sqlplus是什么意思？
	是oracle自带的一个oracle客户端工具，通过客户端自动连接服务端进行sql操作
03）oracle的对安装路径有什么要求，对硬件配置有什么要求？
	不能有中文或者字符串的目录，最好放在盘的根目录下
	硬件要求
		配置至少：
			CPU3核
			内存1G
			硬盘5G	
04）什么是PLSQL？
	过程化结构查询语言，只能在oracle服务器中运行的语言
05）向表中如何插入'引号？
    	''''
	通过以下代码：
	insert into emp(ename) values('''')--'代表转译
06）decode()函数的作用？
	是oracle专用的多条件判断函数，类似sql99case...end
07）union和union all的区别？
	union all表示两个集合中有相同的都取出
	union 表示取一个
08）delete和truncate和drop的区别？
	delete是DML语言可回滚可带where相对慢会产生碎片内容无结构有
	truncate是DDl语言不可回滚不可带where相对中内容无结构有
	drop是DDl语言不可回滚，不可待where内容无，结构无可以带purge关键字
09）oracle中为什么能回滚？
	回收站
	不是说所有的操作都能回滚只限于DML操作的数据暂时放在实例池中
	DML操作后会放入回滚区
	show recyclebin;
10）函数和过程的区别？
	函数：适合于有且只有一个返回值的情况
	过程：适合于无返回值或者两个及其以上的返回值的应用
11）序列的cache有什么作用？
	序列的目的是对主键提供为一个数值，但不一定是连续的
	有了cache机制后，那么主键可以直接从内存中获取，速度提高，默认是20个主键值
	可被多个表共享
12）简述用户，角色和权限的关系？
	多对多的关系
13）谈谈视图使用的场合？
	1.让不同角色用户只能访问该用户可以访问的内容不想让用户查看指定的数据
	2.简化复杂sql的编写，不能提高查询的效率
14）谈谈索引使用的场合？
   	可以针对表中能找个的单个或多个字段创建索引，从而再根据该字段查询
15）谈谈悲观锁和乐观锁

### 为什么需要锁（并发控制）
在多用户环境中，在同一时间可能会有
多个用户更新相同的记录，这会产生冲突。这就是著名的并发性问题。
典型的冲突有：
#### 丢失更新
一个事务的更新覆盖了其它事务的更新结果，就是所谓的更新丢失。例如：用户A把值从6改为2，用户B把值从2改为6，则用户A丢失了他的更新。
#### 脏读
脏读：事务T1更新了一行记录，还未提交所做的修改，这个T2读取了更新后的数据，然后这时T1执行回滚操作，取消刚才的修改，所以T2所读取的行就无效，也就是脏数据。一个事务读取另一个事务尚未提交的修改时，产生脏读很多数据库允许脏读以避免排它锁的竞争。
脏读(Dirty reads)为了解决这些并发带来的问题。 我们需要引入并发控制机制

### 悲观锁
顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次拿数据的时候都会上锁。这样别人拿数据的时候就要等待直到锁的释放。
Oracle的悲观锁需要利用一条现有的Connection，它分成两种方式，从SQL语句的区别来看，就是一种是select for update，一种是select for update nowait的形式。
1. 执行select xxx for update操作时，数据会被锁定，只有执行comit或rollover才会释放
2. 执行select xxx for update nowait操作时，数据也会被锁定，其他人访问时或返回ORA-00054错误，内容是资源正忙，需要采取相应的业务措施进行处理。
缺点
虽然悲观锁应用起来很简单并且十分安全，与此同时却有两大问题：
1. 锁定：应用的使用者选择一个记录进行更新，然后去吃午饭，但是没有结束或者丢弃该事务。这样其他所有需要更新该记录的用户就必须等待正在进行实务操作的用户回来并且完成该事务或者直到DBA杀掉该不愉快的事务并且释放锁。
2. 死锁：用户A和B同时更新数据库。用户A锁定了一条记录并且试图请求用户B持有的锁，同时用户B也在等待获取用户A持有的锁。两个事务同时进入了无限等待状态即进入死锁状态。

结论
1. 如果系统并发量不大且不允许脏读，可以使用悲观锁解决并发问题。
2. 如果系统并发非常大的话，悲观锁会带来很大性能问题，所以一般采用乐观锁。
3. 如果系统读比较多，写比较少，也应该使用乐观锁，可以提高吞吐量。

### 游标/cursor
什么是光标/游标/cursor
类似于JDBC中的ResultSet对象的功能，从上向下依次获取每一记录的内容

使用无参光标cursor，查询所有员工的姓名和工资【如果需要遍历多条记录时，使用光标cursor，无记录找到使用cemp%notfound】
declare
    --定义游标
    cursor cemp is select ename,sal from emp;
    --定义变量
    vename emp.ename%type;
    vsal   emp.sal%type;
begin
    --打开游标，这时游标位于第一条记录之前
    open cemp;
    --循环
    loop
       --向下移动游标一次
       fetch cemp into vename,vsal; 
       --退出循环,当游标下移一次后，找不到记录时，则退出循环
       exit when cemp%notfound;
       --输出结果
       dbms_output.put_line(vename||'--------'||vsal);
    end loop;
    --关闭游标
    close cemp;
end;
/

使用带参光标cursor，查询10号部门的员工姓名和工资
declare
    cursor cemp(pdeptno emp.deptno%type) is select ename,sal from emp where deptno=pdeptno;
    pename emp.ename%type;
    psal emp.sal%type; 
begin 
    open cemp(&deptno);
    loop
        fetch cemp into pename,psal;	 
        exit when cemp%notfound;
        dbms_output.put_line(pename||'的薪水是'||psal);
    end loop;
    close cemp;
end;
/

使用无参光标cursor，真正给员工涨工资，ANALYST涨1000，MANAGER涨800，其它涨400，要求显示编号，姓名，职位，薪水
declare
    cursor cemp is select empno,ename,job,sal from emp;
    pempno emp.empno%type;
    pename emp.ename%type;
    pjob   emp.job%type;
    psal   emp.sal%type;
begin
    open cemp;
    loop
        fetch cemp into pempno,pename,pjob,psal;
        --循环退出条件一定要写
        exit when cemp%notfound;
        if pjob='ANALYST' then
            update emp set sal = sal + 1000 where empno = pempno;
        elsif pjob='MANAGER' then
            update emp set sal = sal + 800 where empno = pempno;
        else 
	    update emp set sal = sal + 400 where empno = pempno;
        end if;
    end loop;
    commit;
    close cemp;
end;
/

### oracle更新date类型数据

update emp set HIREDATE = TO_DATE('2008 05 20','YYYY MM DD') where sal='800';

### 事务
事务这概念是针对数据库来讲的，java在执行数据库操作的时候，需要保证数据完整性、一致性，在增删改的时候要做事务，事务可以说是一个过程。
这个过程只有两个结果，成功或者失败。
而spring中，一般是配置一个事务管理器，结合spring的aop（面向切面）特性，在比如在执行insert、update、delete为前缀或者后缀的方法时，就开启事务，在执行完毕后提交事务。

事务的4大特性
l 原子性：一个交易（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。
l 一致性：在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的默认规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。
l 隔离性：当两个或者多个事务并发访问（此处访问指查询和修改的操作）数据库的同一数据时所表现出的相互关系。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（readcommitted）、可重复读（repeatable read）和串行化（Serializable）。
l 持久性：在事务完成以后，该事务对数据库所作的更改便持久地保存在数据库之中，并且是完全的。


### hibernate的工作流程

當您使用new建構Configuration物件時，會讀取Classpath路徑下的 hibernate.properties，如果您呼叫Configuration的configure()方法沒有指定路徑與檔案名稱時，則會再讀取 Classpath路徑下的hibernate.cfg.xml，如果有與hibernate.properties相同的屬性設定，則 hibernate.cfg.xml中的設定會覆蓋hibernate.properties中的設定。

1.通过Configuration config = new Configuration().configure();//读取并解析hibernate.cfg.xml配置文件
2.由hibernate.cfg.xml中的<mapping resource="com/xx/User.hbm.xml"/>读取并解析映射信息
3.通过SessionFactory sf = config.buildSessionFactory();//创建SessionFactory(buildSessionFactory关联了datasuorce)
//buildSessionFactory关联了hibernate.cfg.xml配置的session-factorys属性
		sf = new Configuration().configure().buildSessionFactory();
4.Session session = sf.openSession();//打开Sesssion
5.Transaction tx = session.beginTransaction();//创建并启动事务Transation
6.persistent operate//操作数据，持久化操作Query q = session.createQuery("from Dept");
7.tx.commit();//提交事务
8.关闭Session
9.关闭SesstionFactory
hibernate.cfg.xml配置文件样式
```
<!DOCTYPE hibernate-configuration PUBLIC
	"-//Hibernate/Hibernate Configuration DTD 3.0//EN"
	"http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
	<!-- 通常，一个session-factory节点代表一个数据库 -->
	<session-factory>
		<!-- 1. 数据库连接配置 -->
		<property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
		<property name="hibernate.connection.url">jdbc:mysql:///hib_demo</property>
		<property name="hibernate.connection.username">root</property>
		<property name="hibernate.connection.password">root</property>
		<!-- 数据库方法配置， hibernate在运行的时候，会根据不同的方言生成符合当前数据库语法的sql -->
		<property name="hibernate.dialect">org.hibernate.dialect.MySQL5Dialect</property>
		<!-- 2. 其他相关配置 -->
		<!-- 2.1 显示hibernate在运行时候执行的sql语句 -->
		<property name="hibernate.show_sql">true</property>
		<!-- 2.2 格式化sql -->
		<property name="hibernate.format_sql">true</property>
		<!-- 2.3 自动建表 -->
		<property name="hibernate.hbm2ddl.auto">update</property>
		<!-- 3. 加载所有映射 <mapping resource="cn/volc/hellohibernate/entity/User.hbm.xml"/> -->
	</session-factory>
</hibernate-configuration>
```
实体映射文件样式
User.hbm.xml
```
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC 
	"-//Hibernate/Hibernate Mapping DTD 3.0//EN"
	"http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
	<!-- 映射文件: 映射一个实体类对象； 描述一个对象最终实现可以直接保存对象数据到数据库中。 -->
	<!-- package: 要映射的对象所在的包(可选,如果不指定,此文件所有的类都要指定全路径) auto-import 默认为true， 在写hql的时候自动导入包名 
		如果指定为false, 再写hql的时候必须要写上类的全名； 如：session.createQuery("from cn.itcast.c_hbm_config.Employee").list(); 
	-->
<hibernate-mapping package="cn.volc.hellohibernate.entity"
	auto-import="true">
	<!-- class 映射某一个对象的(一般情况，一个对象写一个映射文件，即一个class节点) name 指定要映射的对象的类型 table 
		指定对象对应的表； 如果没有指定表名，默认与对象名称一样 
	-->
	<class name="User" table="user">
		<!-- 主键 ，映射 -->
		<id name="id" column="id">
			<!-- 主键的生成策略 identity 自增长(mysql,db2) sequence 自增长(序列)， oracle中自增长是以序列方法实现 
				native 自增长【会根据底层数据库自增长的方式选择identity或sequence】 如果是mysql数据库, 采用的自增长方式是identity 
				如果是oracle数据库， 使用sequence序列的方式实现自增长 increment 自增长(会有并发访问的问题，一般在服务器集群环境使用会存在问题。) 
				assigned 指定主键生成策略为手动指定主键的值 uuid 指定uuid随机生成的唯一的值 foreign (外键的方式， one-to-one讲) -->
			<generator class="uuid" />
		</id>
		<!-- 普通字段映射 property name 指定对象的属性名称 column 指定对象属性对应的表的字段名称，如果不写默认与对象属性一致。 
			length 指定字符的长度, 默认为255 type 指定映射表的字段的类型，如果不指定会匹配属性的类型 java类型： 必须写全名 hibernate类型： 
			直接写类型，都是小写 -->
		<property name="name" column="name" type="java.lang.String"
			length="20"></property>
		<!-- 如果列名称为数据库关键字，需要用反引号或改列名。 -->
		<!-- <property name="desc" column="`desc`" type="java.lang.String"></property> -->
	</class>
</hibernate-mapping>
```

### hibernate映射文件的作用

Hibernate映射文件是Hibernate与数据库进行持久化的桥梁

可根据主键查询(一般是id)，HibernateUtils.getSession.get(clazz, id);//这里的id指的是主键column字段名。
可根据非主键查询
		session = HibernateUtils.getSession();
		tx = session.beginTransaction();
		Query q =session.createQuery("from Employee where empName=?");
		// 注意：参数索引从0开始
		q.setParameter(0, employeeName);

### hibernate的反射

java的反射机制和上一篇JDBC都是hibernate框架的基本要素

反射机制的作用就是能够找到字段、方法和已加载类的构造函数的信息，并且能够应用反射域、方法和构造函数去操作各自的属性。
优点：
1. 对JDBC访问数据库的代码做了封装，大大简化了数据访问层繁琐的重复性代码。
2. Hibernate是一个基于JDBC的主流持久化框架，是一个优秀的ORM(Object-relationship-mapping对象关系映射)实现。他很大程度的简化DAO层的编码工作
3. hibernate使用Java反射机制，而不是字节码增强程序来实现透明性。
5. hibernate的性能非常好，因为它是个轻量级框架。映射的灵活性很出色。它支持各种关系数据库，从一对一到多对多的各种复杂关系。
6. Hibernate 使用 Java 反射机制 而不是字节码增强程序来实现透明性。
7. Hibernate 的性能非常好，因为它是个轻量级框架。 映射的灵活性很出色。
8. 它支持各种关系数据库，从一对一到多对多的各种复杂关系。
9. 在hibernate.hbm.xml中配可以自动创建表
缺点：(Hibernate何时不适用？)
1.hibernate在批量处理数据时具有弱势，使用循环遍历。

### Hibernate是如何延迟加载

Hibernate 的延迟加载（lazy load）是一个被广泛使用的技术。这种延迟加载保证了应用只有在需要时才去数据库中抓取相应的记录。通过延迟加载技术可以避免过多、过早地加载数据表里的数据，从而降低应用的内存开销。Hibernate 的延迟加载本质上就是代理模式的应用，当程序通过 Hibernate 装载一个实体时，默认情况下，Hibernate 并不会立即抓取它的集合属性、关联实体所以对应的记录，而是通过生成一个代理来表示这些集合属性、关联实体，这就是代理模式应用带来的优势。

```
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">
<hibernate-mapping>
	<class name="cn.itcast.nsfw.role.entity.Role" table="role">
		<id name="roleId" type="java.lang.String">
			<column name="role_id" length="32" />
			<generator class="uuid.hex" />
		</id>
		<property name="name" type="java.lang.String">
			<column name="name" length="20" not-null="true" />
		</property>
		<property name="state" type="java.lang.String">
			<column name="state" length="1" />
		</property>
		<set name="rolePrivileges" inverse="true" lazy="false" cascade="save-update,delete">
			<key>
				<column name="role_id"></column>
			</key>
			<one-to-many class="cn.itcast.nsfw.role.entity.RolePrivilege"/>
		</set>
	</class>
</hibernate-mapping>
```
role.hbm.xml放在entity包中

### hibernate缓存机制
1）Hibenate中一级缓存，也叫做session的缓存，它可以在session范围内减少数据库的访问次数！只在session范围有效！ Session关闭，一级缓存失效！
2）当调用session的save/saveOrUpdate/get/load/list/iterator方法的时候，都会把对象放入session的缓存中。 (session.createQurey(“from Dept”))不会放入缓存中
3）Session的缓存由hibernate维护， 用户不能操作缓存内容； 如果想操作缓存内容，必须通过hibernate提供的evit/clear方法操作。
特点：
	只在(当前)session范围有效，作用时间短，效果不是特别明显！
	在短时间内多次操作数据库，效果比较明显！

hibernate的缓存机制？
一级缓存session级别，作用域在session，session关闭缓存消失。
	list()和iterator()查询的数据都可以放入到缓存中。而list()不会从缓存中取出数据
	iterator()会出现N+1条的sql语句。解决方法是使用二级缓存。
二级缓存sessionFactory级别，缓存的是对象，当查询指定的对象的字段时缓存失效
	不会缓存hql语句
查询缓存sessionFactory级别。

一级缓存：
	session级别的，session关闭缓存消失
	list() 默认情况只会放入缓存，不会从一级缓存中取！发送一条sql语句
iterator()典型的N+1条语句。会放入缓存，也会从缓冲区中取数据。发出一条sql语句取所有数据的id值，再根据id发出sql语句才真正查询数据对象。
	使用查询缓存，可以让list()查询从二级缓存中取！
二级缓存：
	sessionFactory级别
二级缓存的使用：
1.导包
hibernate对于二级缓存没有相关的组件，要加入额外的二级缓存包相应的配置。常用的二级缓存包是EHcache
这个我们在下载好的hibernate的lib->optional->ehcache下可以找到(我这里使用的hibernate4.1.7版本)，然后将里面的几个jar包导入即可。
2.在hibernate.cfg.xml配置文件中配置我们二级缓存的一些属性：
```
<!-- 开启二级缓存 -->
        <property name="hibernate.cache.use_second_level_cache">true</property>
        <!-- 二级缓存的提供类 在hibernate4.0版本以后我们都是配置这个属性来指定二级缓存的提供类-->
        <property name="hibernate.cache.region.factory_class">org.hibernate.cache.ehcache.EhCacheRegionFactory</property>
        <!-- 二级缓存配置文件的位置 -->
        <property name="hibernate.cache.provider_configuration_file_resource_path">ehcache.xml</property>
```

```
<!--这个类在4.0版本以后已经不建议被使用了-->
<property name="hibernate.cache.provider_class">net.sf.ehcache.hibernate.EhCacheProvider</property>
```
3.配置hibernate的二级缓存是通过使用 ehcache的缓存包，所以我们需要创建一个 ehcache.xml 的配置文件，来配置我们的缓存信息，将其放到项目根目录下
```
<ehcache>
    <!-- Sets the path to the directory where cache .data files are created.
         If the path is a Java System Property it is replaced by
         its value in the running VM.
         The following properties are translated:
         user.home - User's home directory
         user.dir - User's current working directory
         java.io.tmpdir - Default temp file path -->
　　<!--指定二级缓存存放在磁盘上的位置-->
    <diskStore path="user.dir"/>　　
　　<!--我们可以给每个实体类指定一个对应的缓存，如果没有匹配到该类，则使用这个默认的缓存配置-->
    <defaultCache
        maxElementsInMemory="10000"　　//在内存中存放的最大对象数
        eternal="false"　　　　　　　　　//是否永久保存缓存，设置成false
        timeToIdleSeconds="120"　　　　
        timeToLiveSeconds="120"　　　　
        overflowToDisk="true"　　　　　//如果对象数量超过内存中最大的数，是否将其保存到磁盘中，设置成true
        />
　　<!--
　　　　1、timeToLiveSeconds的定义是：以创建时间为基准开始计算的超时时长；
　　　　2、timeToIdleSeconds的定义是：在创建时间和最近访问时间中取出离现在最近的时间作为基准计算的超时时长；
　　　　3、如果仅设置了timeToLiveSeconds，则该对象的超时时间=创建时间+timeToLiveSeconds，假设为A；
　　　　4、如果没设置timeToLiveSeconds，则该对象的超时时间=max(创建时间，最近访问时间)+timeToIdleSeconds，假设为B；
　　　　5、如果两者都设置了，则取出A、B最少的值，即min(A,B)，表示只要有一个超时成立即算超时。
　　-->
　　<!--可以给每个实体类指定一个配置文件，通过name属性指定，要使用类的全名-->
    <cache name="com.xiaoluo.bean.Student"
        maxElementsInMemory="10000"
        eternal="false"
        timeToIdleSeconds="300"
        timeToLiveSeconds="600"
        overflowToDisk="true"
        />
    <cache name="sampleCache2"
        maxElementsInMemory="1000"
        eternal="true"
        timeToIdleSeconds="0"
        timeToLiveSeconds="0"
        overflowToDisk="false"
        /> -->
</ehcache>
```
4.开启我们的二级缓存	
①如果使用xml配置，我们需要在 Student.hbm.xml 中加上一下配置：
```
<hibernate-mapping package="com.xiaoluo.bean">
    <class name="Student" table="t_student">
        <!-- 二级缓存一般设置为只读的 -->
        <cache usage="read-only"/>
        <id name="id" type="int" column="id">
            <generator class="native"/>
        </id>
        <property name="name" column="name" type="string"></property>
        <property name="sex" column="sex" type="string"></property>
        <many-to-one name="room" column="rid" fetch="join"></many-to-one>
    </class>
</hibernate-mapping>
```
二级缓存的使用策略一般有这几种：read-only、nonstrict-read-write、read-write、transactional。注意：我们通常使用二级缓存都是将其配置成 read-only ，即我们应当在那些不需要进行修改的实体类上使用二级缓存，否则如果对缓存进行读写的话，性能会变差，这样设置缓存就失去了意义。

②如果使用annotation配置，我们需要在Student这个类上加上这样一个注解：
```
@Entity
@Table(name="t_student")
@Cache(usage=CacheConcurrencyStrategy.READ_ONLY)　　//　　表示开启二级缓存，并使用read-only策略
public class Student
{
    private int id;
    private String name;
    private String sex;
    private Classroom room;
    .......
}
```
①二级缓存是sessionFactory级别的缓存
```
try
        {
            session = HibernateUtil.openSession();

            Student stu = (Student) session.load(Student.class, 1);
            System.out.println(stu.getName() + "-----------");
        }
        catch (Exception e)
        {
            e.printStackTrace();
        }
        finally
        {
            HibernateUtil.close(session);
        }
        try
        {
            /**
             * 即使当session关闭以后，因为配置了二级缓存，而二级缓存是sessionFactory级别的，所以会从缓存中取出该数据
             * 只会发出一条sql语句
             */
            session = HibernateUtil.openSession();
            Student stu = (Student) session.load(Student.class, 1);
            System.out.println(stu.getName() + "-----------");
            /**
             * 因为设置了二级缓存为read-only，所以不能对其进行修改
             */
            session.beginTransaction();
            stu.setName("aaa");
            session.getTransaction().commit();
        }
```
②二级缓存缓存的仅仅是对象，如果查询出来的是对象的一些属性，则不会被加到缓存中去
```
try
        {
            session = HibernateUtil.openSession();

            /**
             * 注意：二级缓存中缓存的仅仅是对象，而下面这里只保存了姓名和性别两个字段，所以 不会被加载到二级缓存里面
             */
            List<Object[]> ls = (List<Object[]>) session
                    .createQuery("select stu.name, stu.sex from Student stu")
                    .setFirstResult(0).setMaxResults(30).list();
        }
        catch (Exception e)
        {
            e.printStackTrace();
        }
        finally
        {
            HibernateUtil.close(session);
        }
        try
        {
            /**
             * 由于二级缓存缓存的是对象，所以此时会发出两条sql
             */
            session = HibernateUtil.openSession();
            Student stu = (Student) session.load(Student.class, 1);
            System.out.println(stu);
        }
```
我们看到这个测试用例，如果我们只是取出对象的一些属性的话，则不会将其保存到二级缓存中去，因为二级缓存缓存的仅仅是对象。

③通过二级缓存来解决 N+1 的问题
```
try
        {
            session = HibernateUtil.openSession();
            /**
             * 将查询出来的Student对象缓存到二级缓存中去
             */
            List<Student> stus = (List<Student>) session.createQuery(
                    "select stu from Student stu").list();
        }
        catch (Exception e)
        {
            e.printStackTrace();
        }
        finally
        {
            HibernateUtil.close(session);
        }
        try
        {
            /**
             * 由于学生的对象已经缓存在二级缓存中了，此时再使用iterate来获取对象的时候，首先会通过一条
             * 取id的语句，然后在获取对象时去二级缓存中，如果发现就不会再发SQL，这样也就解决了N+1问题 
             * 而且内存占用也不多
             */
            session = HibernateUtil.openSession();
            Iterator<Student> iterator = session.createQuery("from Student")
                    .iterate();
            for (; iterator.hasNext();)
            {
                Student stu = (Student) iterator.next();
                System.out.println(stu.getName());
            }
        }
```
当我们如果需要查询出两次对象的时候，可以使用二级缓存来解决N+1的问题。

④二级缓存会缓存 hql 语句吗？
```
try
        {
            session = HibernateUtil.openSession();
            List<Student> ls = session.createQuery("from Student")
                    .setFirstResult(0).setMaxResults(50).list();//第一次查询
        }
        catch (Exception e)
        {
            e.printStackTrace();
        }
        finally
        {
            HibernateUtil.close(session);
        }
        try
        {
            /**
             * 使用List会发出两条一模一样的sql，此时如果希望不发sql就需要使用查询缓存
             */
            session = HibernateUtil.openSession();
            List<Student> ls = session.createQuery("from Student")
                    .setFirstResult(0).setMaxResults(50).list();//第二次查询
            Iterator<Student> stu = ls.iterator();
            for(;stu.hasNext();)
            {
                Student student = stu.next();
                System.out.println(student.getName());
            }
        }
```
结果
```
Hibernate: select student0_.id as id2_, student0_.name as name2_, student0_.sex as sex2_, student0_.rid as rid2_ from t_student student0_ limit ?
Hibernate: select student0_.id as id2_, student0_.name as name2_, student0_.sex as sex2_, student0_.rid as rid2_ from t_student student0_ limit ?
```

查询缓存(sessionFactory级别)
我们如果要配置查询缓存，只需要在hibernate.cfg.xml中加入一条配置即可：
```
　<!-- 开启查询缓存 -->
        <property name="hibernate.cache.use_query_cache">true</property>
```
然后我们如果在查询hql语句时要使用查询缓存，就需要在查询语句后面设置这样一个方法:
```
List<Student> ls = session.createQuery("from Student where name like ?")
                    .setCacheable(true)　　//开启查询缓存，查询缓存也是SessionFactory级别的缓存
                    .setParameter(0, "%王%")
                    .setFirstResult(0).setMaxResults(50).list();
```
如果是在annotation中，我们还需要在这个类上加上这样一个注解：@Cacheable
接下来我们来通过测试用例来看看我们的查询缓存
①查询缓存也是sessionFactory级别的缓存
```
@Test
    public void test2() {
        Session session = null;
        try {
            /**
             * 此时会发出一条sql取出所有的学生信息
             */
            session = HibernateUtil.openSession();
            List<Student> ls = session.createQuery("from Student")
                    .setCacheable(true)  //开启查询缓存,查询缓存也是sessionFactory级别的缓存
                    .setFirstResult(0).setMaxResults(50).list();
            Iterator<Student> stus = ls.iterator();
            for(;stus.hasNext();) {
                Student stu = stus.next();
                System.out.println(stu.getName());
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            HibernateUtil.close(session);
        }
        try {
            /**
             * 此时会发出一条sql取出所有的学生信息
             */
            session = HibernateUtil.openSession();
            List<Student> ls = session.createQuery("from Student")
                    .setCacheable(true)  //开启查询缓存,查询缓存也是sessionFactory级别的缓存
                    .setFirstResult(0).setMaxResults(50).list();
            Iterator<Student> stus = ls.iterator(); 
            for(;stus.hasNext();) {
                Student stu = stus.next();
                System.out.println(stu.getName());
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            HibernateUtil.close(session);
        }
    }
```
结果
```
Hibernate: select student0_.id as id2_, student0_.name as name2_, student0_.sex as sex2_, student0_.rid as rid2_ from t_student student0_ limit ?
```

我们看到，此时如果我们发出两条相同的语句，hibernate也只会发出一条sql，因为已经开启了查询缓存了，并且查询缓存也是sessionFactory级别的

②只有当 HQL 查询语句完全相同时，连参数设置都要相同，此时查询缓存才有效
```
　@Test
    public void test3() {
        Session session = null;
        try {
            /**
             * 此时会发出一条sql取出所有的学生信息
             */
            session = HibernateUtil.openSession();
            List<Student> ls = session.createQuery("from Student where name like ?")
                    .setCacheable(true)//开启查询缓存，查询缓存也是SessionFactory级别的缓存
                    .setParameter(0, "%王%")
                    .setFirstResult(0).setMaxResults(50).list();
            Iterator<Student> stus = ls.iterator();
            for(;stus.hasNext();) {
                Student stu = stus.next();
                System.out.println(stu.getName());
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            HibernateUtil.close(session);
        }
        session = null;
        try {
            /**
             * 此时会发出一条sql取出所有的学生信息
             */
            session = HibernateUtil.openSession();
            /**
             * 只有当HQL完全相同的时候，连参数都要相同，查询缓存才有效
             */
//            List<Student> ls = session.createQuery("from Student where name like ?")
//                    .setCacheable(true)//开启查询缓存，查询缓存也是SessionFactory级别的缓存
//                    .setParameter(0, "%王%")
//                    .setFirstResult(0).setMaxResults(50).list();
            List<Student> ls = session.createQuery("from Student where name like ?")
                    .setCacheable(true)//开启查询缓存，查询缓存也是SessionFactory级别的缓存
                    .setParameter(0, "%张%")
                    .setFirstResult(0).setMaxResults(50).list();
            Iterator<Student> stus = ls.iterator();
            for(;stus.hasNext();) {
                Student stu = stus.next();
                System.out.println(stu.getName());
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            HibernateUtil.close(session);
        }
    }
```
结果
```
Hibernate: select student0_.id as id2_, student0_.name as name2_, student0_.sex as sex2_, student0_.rid as rid2_ from t_student student0_ where student0_.name like ? limit ?

Hibernate: select student0_.id as id2_, student0_.name as name2_, student0_.sex as sex2_, student0_.rid as rid2_ from t_student student0_ where student0_.name like ? limit ?
```

我们看到，如果我们的hql查询语句不同的话，我们的查询缓存也没有作用

③查询缓存也能引起 N+1 的问题
查询缓存也能引起 N+1 的问题，我们这里首先先将 Student 对象上的二级缓存先注释掉：
```
　<!-- 二级缓存一般设置为只读的 -->
        <!--　　<cache usage="read-only"/>　　-->
```
test
```
@Test
    public void test4() {
        Session session = null;
        try {
            /**
             * 查询缓存缓存的不是对象而是id
             */
            session = HibernateUtil.openSession();
            List<Student> ls = session.createQuery("from Student where name like ?")
                    .setCacheable(true)//开启查询缓存，查询缓存也是SessionFactory级别的缓存
                    .setParameter(0, "%王%")
                    .setFirstResult(0).setMaxResults(50).list();
            Iterator<Student> stus = ls.iterator();
            for(;stus.hasNext();) {
                Student stu = stus.next();
                System.out.println(stu.getName());
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            HibernateUtil.close(session);
        }
        
        session = null;
        try {
            /**
             * 查询缓存缓存的是id，此时由于在缓存中已经存在了这样的一组学生数据，但是仅仅只是缓存了
             * id，所以此处会发出大量的sql语句根据id取对象，这也是发现N+1问题的第二个原因
             * 所以如果使用查询缓存必须开启二级缓存
             */
            session = HibernateUtil.openSession();
            List<Student> ls = session.createQuery("from Student where name like ?")
                    .setCacheable(true)//开启查询缓存，查询缓存也是SessionFactory级别的缓存
                    .setParameter(0, "%王%")
                    .setFirstResult(0).setMaxResults(50).list();
            Iterator<Student> stus = ls.iterator();
            for(;stus.hasNext();) {
                Student stu = stus.next();
                System.out.println(stu.getName());
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            HibernateUtil.close(session);
        }
    }
```
结果
```
Hibernate: select student0_.id as id2_, student0_.name as name2_, student0_.sex as sex2_, student0_.rid as rid2_ from t_student student0_ where student0_.name like ? limit ?

Hibernate: select student0_.id as id2_2_, student0_.name as name2_2_, student0_.sex as sex2_2_, student0_.rid as rid2_2_, classroom1_.id as id1_0_, classroom1_.name as name1_0_, classroom1_.sid as sid1_0_, special2_.id as id0_1_, special2_.name as name0_1_, special2_.type as type0_1_ from t_student student0_ left outer join t_classroom classroom1_ on student0_.rid=classroom1_.id left outer join t_special special2_ on classroom1_.sid=special2_.id where student0_.id=?
Hibernate: select student0_.id as id2_2_, student0_.name as name2_2_, student0_.sex as sex2_2_, student0_.rid as rid2_2_, classroom1_.id as id1_0_, classroom1_.name as name1_0_, classroom1_.sid as sid1_0_, special2_.id as id0_1_, special2_.name as name0_1_, special2_.type as type0_1_ from t_student student0_ left outer join t_classroom classroom1_ on student0_.rid=classroom1_.id left outer join t_special special2_ on classroom1_.sid=special2_.id where student0_.id=?
Hibernate: select student0_.id as id2_2_, student0_.name as name2_2_, student0_.sex as sex2_2_, student0_.rid as rid2_2_, classroom1_.id as id1_0_, classroom1_.name as name1_0_, classroom1_.sid as sid1_0_, special2_.id as id0_1_, special2_.name as name0_1_, special2_.type as type0_1_ from t_student student0_ left outer join t_classroom classroom1_ on student0_.rid=classroom1_.id left outer join t_special special2_ on classroom1_.sid=special2_.id where student0_.id=?
Hibernate: select student0_.id as id2_2_, student0_.name as name2_2_, student0_.sex as sex2_2_, student0_.rid as rid2_2_, classroom1_.id as id1_0_, classroom1_.name as name1_0_, classroom1_.sid as sid1_0_, special2_.id as id0_1_, special2_.name as name0_1_, special2_.type as type0_1_ from t_student student0_ left outer join t_classroom classroom1_ on student0_.rid=classroom1_.id left outer join t_special special2_ on classroom1_.sid=special2_.id where student0_.id=?
```
.........................

我们看到，当我们将二级缓存注释掉以后，在使用查询缓存时，也会出现 N+1 的问题，为什么呢？

因为查询缓存缓存的也仅仅是对象的id，所以第一条 sql 也是将对象的id都查询出来，但是当我们后面如果要得到每个对象的信息的时候，此时又会发sql语句去查询，所以，如果要使用查询缓存，我们一定也要开启我们的二级缓存，这样就不会出现 N+1 问题了

### Hibernate自带的分页机制是什么？如果不使用Hibernate自带的分页，则采用什么方式分页？

　　1、hibernate自带的分页机制：获得Session对象后，从Session中获得Query对象。用Query.setFirstResult()：设置要显示的第一行数据, 
Query.setMaxResults()：设置要显示的最后一行数据。 
　　2、不使用hibernate自带的分页,可采用sql语句分页， 
如：5:为每页显示的记录,2为当前页： select * top 5 from table where tabId not in (select tabId top (2-1)*5 from table);

### spring配置数据库
1. 使用3cp0jar包，直接配置dataSource
```
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource"       
        destroy-method="close">      
    <property name="driverClass" value=" oracle.jdbc.driver.OracleDriver "/>      
    <property name="jdbcUrl" value=" jdbc:oracle:thin:@localhost:1521:ora9i "/>      
    <property name="user" value="admin"/>      
    <property name="password" value="1234"/>      
</bean>
```
2.引用db.properties文件
```
<!-- 导入外部的properties配置文件 -->
	<context:property-placeholder location="classpath:db.properties" />

	<!-- 配置c3p0数据源 -->
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource" destroy-method="close">
		<property name="jdbcUrl" value="${jdbcUrl}"></property>
		<property name="driverClass" value="${driverClass}"></property>
		<property name="user" value="${user}"></property>
		<property name="password" value="${password}"></property>
		<!--初始化时获取三个连接，取值应在minPoolSize与maxPoolSize之间。Default: 3 -->
		<property name="initialPoolSize" value="${initialPoolSize}"></property>
		<!--连接池中保留的最小连接数。Default: 3 -->
		<property name="minPoolSize" value="3"></property>
		<!--连接池中保留的最大连接数。Default: 15 -->
		<property name="maxPoolSize" value="${maxPoolSize}"></property>
		<!--当连接池中的连接耗尽的时候c3p0一次同时获取的连接数。Default: 3 -->
		<property name="acquireIncrement" value="3"></property>
		<!--最大空闲时间，1800秒内未使用则连接被丢弃，若为0则永不丢弃。Default: 0 -->
	    <property name="maxIdleTime" value="1800"></property>
	</bean>

	<bean id="sessionFactory" class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">
		<property name="dataSource" ref="dataSource"></property>
		<property name="hibernateProperties">
			<props>
				<prop key="hibernate.dialect">org.hibernate.dialect.MySQL5Dialect</prop>
				<prop key="hibernate.show_sql">true</prop>
				<prop key="hibernate.hbm2ddl.auto">update</prop>
				<prop key="javax.persistence.validation.mode">none</prop>
			</props>
		</property>
		<property name="mappingLocations">
			<list>
				<value>classpath:cn/itcast/nsfw/*/entity/*.hbm.xml</value>
				<value>classpath:cn/itcast/test/entity/*.hbm.xml</value>
			</list>
		</property>
	</bean>
```

2.引入hibernate.cfg.xml文件
```
	<!-- 配置 sessionFactory -->
	   <bean id="sessionFactory" class ="org.springframework.orm.hibernate3.LocalSessionFactoryBean">
              <property name="configLocation">
                     <value>classpath:hibernate.cfg .xml</value>
              </property>
              <!-- <property name="configLocation" value ="classpath:hibernate.cfg.xml" >-->
              </property>
       </bean>      
       <!-- 配置事务管理器 -->   
       <bean id ="transactionManager" class ="org.springframework.orm.hibernate3.HibernateTransactionManager" >
              <property name = sessionFactory" >
                     <ref local = "sessionFactory" />
              </property>
       </bean>
```
Json数据
```java
	private static void map2json() {
		List<City> cityList = new ArrayList<City>();
		cityList.add(new City(1,"中山"));
		cityList.add(new City(2,"佛山"));
		Map<String,Object> map = new LinkedHashMap<String,Object>();
		map.put("total",cityList.size());//表示集合的长度
		map.put("rows",cityList);//rows表示集合
		JSONArray jSONArray = JSONArray.fromObject(map);
		String jsonJAVA = jSONArray.toString();
		System.out.println(jsonJAVA);
		//[{"total":2,"rows":[{"id":1,"name":"中山"},{"id":2,"name":"佛山"}]}]
		jsonJAVA = jsonJAVA.substring(1,jsonJAVA.length()-1);
		System.out.println(jsonJAVA);
	}
```

GetDataAction.java
```java

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.apache.struts2.ServletActionContext;
import com.opensymphony.xwork2.ActionSupport;
public class GetDataAction extends ActionSupport{

	private User user;
	public String getData() throws IOException{//不能传参
		HttpServletRequest request = ServletActionContext.getRequest();
		System.out.println("进入action");
		String id = request.getParameter("id");
		user = new User();
		user.setId(id);
		user.setUserName("张三");
		System.out.println(user);
		HttpServletResponse response = ServletActionContext.getResponse();
		//PrintWriter writer = response.getWriter();
		ServletOutputStream outputStream = response.getOutputStream();
		//writer.write("good");//会直接返回浏览器页面
		outputStream.write("good".getBytes("utf-8"));//不管是printwriter还是outputstream都可以向浏览器发送数据，ajax都可以接收
		//writer.close();
		outputStream.close();
		return SUCCESS;//如果没有jsp页面接受json格式的数据会直接在浏览器页面打印出json格式数据
	}
	public User getUser() {
		return user;
	}
	
}
```
getdatabypost.jsp
```html
<%@ page language="java" import="java.util.*" pageEncoding="utf-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<title>获取json对象</title>
<script type="text/javascript" src="../js/jquery-1.8.2.min.js"></script>
<head>
</head>
<body>
	<input type="button" value="获取数据" />
</body>
<script type="text/javascript">
	$(":button").click(function(){
		alert(0);
		$.post(
			"${pageContext.request.contextPath}/getData_getData.action?id=1",
			function(data,status,ajax){
					//ajax是json文本{"data":"",...}字符串,data是json文本的对象
					//在action中javabean要提供get方法ajax才能获得json格式的javabean(user)
				alert(ajax.responseText);//打印出{"data":"success","user":{"id":null,"userName":"张三"}}
				var user = data.user;
				alert(user);
				alert(user.id);
			}
		);
	});
</script>
</html>
```
```html
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
          "-//Apache Software Foundation//DTD Struts Configuration 2.0//EN"
          "http://struts.apache.org/dtds/struts-2.0.dtd">
<struts>
	<package name="firstStruts" extends="json-default">
		<action name="helloStruts" class="cn.volc.action.FirstStruts"
			method="execute">
			<result name="success">/jsp/success.jsp</result>
		</action>
		<action name="getData_*" class="cn.volc.action.GetDataAction"
			method="{1}">
			<!-- 
			<result name="success" type="json" />
			 -->
			<result name="success">/jsp/getdatabypost.jsp</result>
		</action>
	</package>
</struts>
```
异步发布信息
```
//异步发布信息，无需返回值
	public void publicInfo(){
		try {
			if(info != null){
				//1、更新信息状态
				Info tem = infoService.findObjectById(info.getInfoId());
				tem.setState(info.getState());
				infoService.update(tem);
				//2、输出更新结果
				HttpServletResponse response = ServletActionContext.getResponse();
				response.setContentType("text/html");
				ServletOutputStream outputStream = response.getOutputStream();
				outputStream.write("更新状态成功".getBytes("utf-8"));
				outputStream.close();
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
```
listUI.JSP
```
//异步发布信息,信息的id及将要改成的信息状态
  	function doPublic(infoId, state){
  		//1、更新信息状态
  		$.ajax({
  			url:"${basePath}nsfw/info_publicInfo.action",
  			data:{"info.infoId":infoId, "info.state":state},
  			type:"post",
  			success: function(msg){
  				//2、更新状态栏、操作栏的显示值
  				if("更新状态成功" == msg){
  					if(state == 1){//说明信息状态已经被改成 发布，状态栏显示 发布，操作栏显示 停用
  						$("#show_"+infoId).html("发布");
  						$("#oper_"+infoId).html('<a href="javascript:doPublic(\''+infoId+'\',0)">停用</a>');
  					} else {
  						$("#show_"+infoId).html("停用");
  						$("#oper_"+infoId).html('<a href="javascript:doPublic(\''+infoId+'\',1)">发布</a>');
  					}
  				} else {alert("更新信息状态失败！");}
  			},
  			error: function(){
  				alert("更新信息状态失败！");
  			}
  		});
```

### springmvc的工作流程
![s](https://volc1612.gitee.io/blog/images/interview-question/s.png)
![t](https://volc1612.gitee.io/blog/images/interview-question/t.png)

Spring工作流程描述

1. 用户向服务器发送请求，请求被Spring 前端控制Servelt DispatcherServlet捕获；
      2. DispatcherServlet对请求URL进行解析，得到请求资源标识符（URI）。然后根据该URI，调用HandlerMapping(映射器)获得该Handler配置的所有相关的对象（包括Handler对象以及(加载controllerRequestMapping注解信息)Handler对象对应的拦截器），最后以HandlerExecutionChain(执行链)对象的形式返回，把handler交给核心控制器；
      3. DispatcherServlet 根据获得的Handler，选择一个合适的HandlerAdapter(适配器)。（附注：如果成功获得HandlerAdapter后，此时将开始执行拦截器的preHandler(...)方法）
       4.  提取Request中的模型数据，填充Handler入参，开始执行Handler（Controller)。 在填充Handler的入参过程中，根据你的配置，Spring将帮你做一些额外的工作：
      HttpMessageConveter： 将请求消息（如Json、xml等数据）转换成一个对象，将对象转换为指定的响应信息
      数据转换：对请求消息进行数据转换。如String转换成Integer、Double等
      数据根式化：对请求消息进行数据格式化。 如将字符串转换成格式化数字或格式化日期等
      数据验证： 验证数据的有效性（长度、格式等），验证结果存储到BindingResult或Error中
      5.  Handler执行完成后，向DispatcherServlet 返回一个ModelAndView对象；
      6.  根据返回的ModelAndView，选择一个适合的ViewResolver（必须是已经注册到Spring容器中的ViewResolver)返回给DispatcherServlet ；
      7. ViewResolver 结合Model和View，来渲染视图
      8. 将渲染结果返回给客户端。

其他流程图解
![u](https://volc1612.gitee.io/blog/images/interview-question/u.png)

(1).发起请求到前端控制器(DispatcherServlet);
(2).前端控制器请求HandlerMapping查找Handler，可以根据xml配置、注解进行查找；
(3).处理器映射器HandlerMapping向前端控制器返回Handler；
(4).前端控制器调用处理器适配器去执行Handler(包含的controller注解的信息)；
(5).处理器适配器去执行Handler；
(6).Handler执行完成给适配器返回ModelAndView；
(7).处理器适配器向前端控制器返回ModelAndView(是springmvc框架的一个底层对象，包括Model和View)；
(8).前端控制器请求视图解析器去进行视图解析，根据逻辑视图名称解析真正的视图(jsp...)；
(9).视图解析器向前端控制器返回View;
(10).前端控制器进行视图渲染，视图渲染就是将模型数据(在ModelAndView对象中)填充到request域中。
(11).前端控制器向用户响应结果。

面试回答的流程：
1. 用户向服务器发送请求，请求被Spring 前端控制Servelt DispatcherServlet捕获；
2. DispatcherServlet对请求URL进行解析，得到请求资源标识符（URI）。然后根据该URI，调用HandlerMapping(映射器)获得该Handler配置的所有相关的对象（包括Handler对象以及加载controllerRequestMapping注解信息)，最后以HandlerExecutionChain(执行链)对象的形式返回，把handler交给核心控制器。
3. DispatcherServlet 根据获得的Handler，选择一个合适的HandlerAdapter(适配器)，并获取适配器。
4. DispatcherServlet 调用适配器去执行Handler(包含的controller注解的信息)；
5.  Handler执行完成后，向DispatcherServlet 返回一个ModelAndView对象(向视图发送的数据和视图的逻辑名字)；
6. DispatcherServlet根据返回的ModelAndView，选择一个适合的ViewResolver(视图解析器)返回给DispatcherServlet ；
7. DispatcherServlet请求视图解析器去进行视图解析，根据逻辑视图名称解析真正的视图(jsp...)。返回一个View给DispatcherServlet。
8. 前端控制器进行视图渲染，视图渲染就是将模型数据(在ModelAndView对象中)填充到request域中。
9. 前端控制器向用户响应结果。


Spring工作流程描述
    为什么Spring只使用一个Servlet(DispatcherServlet)来处理所有请求？
     详细见J2EE设计模式-前端控制模式
    Spring为什么要结合使用HandlerMapping以及HandlerAdapter来处理Handler?
    符合面向对象中的单一职责原则，代码架构清晰，便于维护，最重要的是代码可复用性高。如HandlerAdapter可能会被用于处理多种Handler。


1.客户端发送请求，符合.action的话，就有dispatchservlet来处理
	1.1.交给映射器，交给action处理
2.映射器根据客户的http请求，在对比<bean name=”/hello.action” class=”…”>如果匹配正确，再讲http请求交给
3.执行Action中的业务方法，最总发挥一个名叫ModelAndView的对象，其中封装了，向视图发送的数据和视图的逻辑名字。
4.ModelAndView对象随着响应到达核心dispatchservlet中
5.这时DispatcherServlet收到这个对象，它也不知道视图逻辑名是何意，有的未退一个名叫视图解析器对象，具体解析modelandview中的内容。
6.将试图解析后的内容交给dispatchservlet核心控制器对象，进行视图渲染，将数据填充到request与对象中，请求转发到具体的视图页面。取出数据进行再显示给用户。
 
![v](https://volc1612.gitee.io/blog/images/interview-question/v.png)
![w](https://volc1612.gitee.io/blog/images/interview-question/w.png)
![x](https://volc1612.gitee.io/blog/images/interview-question/x.png)
优点：
1. 提供了一种管理对象的方法，可以把中间层对象有效地组织起来。一个完美的框架“黏合剂”。
2. 采用了分层结构，可以增量引入到项目中。
3. 有利于面向接口编程习惯的养成。
4. 目的之一是为了写出易于测试的代码。
5. 非侵入性，应用程序对Spring API的依赖可以减至最小限度。
6. 一致的数据访问介面。
缺点：
1，Spring的使用引入了新的复杂度，这点毋庸置疑（虽然可降低已有复杂度，但是小型项目引入Spring简直是噩梦）。 
小型项目必需要使用spring吗？ 其实也不能论大小来说，当你想要方便地使用某些特性的时候，比如说ioc，切面，声明式事务等等，那就应该用spring，你都需要那么多spring能轻易集成并且串联的功能，为什么还要不使用spring呢？
当然如果项目用不上这些特性，也没说一定要用spring。
2，破坏了一些本来不该破坏的结构，比如一个类的完整抽象，现在需要通过看配置文件或Annotation才能完全理解, 
这是ioc容器共有的特征，当然，一个好的ide，比如IntelliJ IDEA 在：重构、导航方面，你几乎不会感觉到因为使用ioc而带来的不便。
3，单个功能的测试必须启动Spring容器。 
启动慢了点，不过在编码上用Spring JUnit Runner注解也不会多敲多少代码
spring和springmvc中的配置
spring
加载applicationContext.xml方法(应用上下文配置)
```
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:applicationContext.xml</param-value>
	</context-param>
```

applicationContext.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
	<!-- ###############对象创建############### -->
	<bean id="user1" class="cn.volc.spring.User"></bean>
</beans>
```
springmvc配置
spring.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="
	  http://www.springframework.org/schema/beans 
	  http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
	  http://www.springframework.org/schema/context
      http://www.springframework.org/schema/context/spring-context-3.0.xsd
      http://www.springframework.org/schema/mvc
      http://www.springframework.org/schema/mvc/spring-mvc-3.0.xsd
      ">
	<!-- 扫描指定包中的action -->
	<context:component-scan base-package="cn.volc.springmvc" ></context:component-scan>
	<!-- 基于注解的映射器(可选) -->
	<bean
	class="org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping" />
	<!—-适配器 -->
	<bean
	class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter">
		<property name="messageConverters">
			<list>
				<bean
	class="org.springframework.http.converter.json.MappingJacksonHttpMessageConverter" />
			</list>
		</property>
	</bean>
	<!-- 视图解析器(可选) -->
	<bean
	class="org.springframework.web.servlet.view.InternalResourceViewResolver" />
</beans>
```
需要配置web.xml中核心过滤器(servlet)
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">
<!-- 配置springmvc核心控制器 -->
<servlet>
	<servlet-name>DispatcherServlet</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	<init-param><!—这个配置是在servlet中进行配置与context-parm不同 -->
		<param-name>ContextConfigLocation</param-name>
		<param-value>classpath:spring.xml</param-value>
	</init-param>
</servlet>
<servlet-mapping>
	<servlet-name>DispatcherServlet</servlet-name>
	<url-pattern>*.action</url-pattern>
</servlet-mapping>
  <display-name>helloSpringMVCzhujie2</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
</web-app>
```

视图解析器InternalResourceViewResolver【解析视图逻辑名对应的真实路径】
ModelAndView对象中即可以封装真实视图路径名，也可以封装视图路径的逻辑名，springmvc.xml
代码如下：
```
<!-- 视图解析器(框架) -->  
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    	<property name="prefix" value="/jsp/"/>
    	<property name="suffix" value=".jsp"/>
    </bean>
```
Action类代码如下：
modelAndView.setViewName("success");
映射器Mapping【什么样的请求交给Action】
1）	org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping(核心)
将程序员定义的Action所对应的<bean>标签的name属性作为请求路径
```
  <!-- 注册控制器(程序员) -->
      <bean name="/add.action" class="cn.itcast.javaee.springmvc.mapping.UserAction"></bean>

      <!-- 注册映射器(handler包)(框架) -->
	  <bean 
class="org.springframework.web.servlet
.handler
.BeanNameUrlHandlerMapping"></bean>
```
2）	org.springframework.web.servlet.handler.SimpleUrlHandlerMapping
/delete.action和/update.action和/find.action请求路径都交由<bean>标签为id的Action，即
多个路径对应同一个Action
```
  <!-- 注册控制器(程序员) -->
	  <bean id="userActionID" class="cn.itcast.javaee.springmvc.mapping.UserAction"></bean>
		
	  <!-- 注册映射器(handler包)(框架) -->
	  <bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
	  		<property name="mappings">
	  			<props>
	  				<prop key="/delete.action">userActionID</prop>
	  				<prop key="/update.action">userActionID</prop>
	  				<prop key="/find.action">userActionID</prop>
	  			</props>
	  		</property>
	  </bean>
```
适配器Adapter【Action实现什么接口】
1）	Action实现Controller接口
```
org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter
public class EmpAction implements Controller{
	public ModelAndView handleRequest(HttpServletRequest request,HttpServletResponse response) throws Exception {
		System.out.println("EmpAction::handleRequest");
		request.setCharacterEncoding("UTF-8");
		String username = request.getParameter("username");
		ModelAndView modelAndView = new ModelAndView();
		modelAndView.addObject("message",username);
		modelAndView.setViewName("/jsp/success.jsp");
		return modelAndView;
```
控制器Controller【Action继承什么类】
1.org.springframework.web.servlet.mvc.ParameterizableViewController
如果请求是/hello.action的请求路径，则直接跳转到/jsp/success.jsp页面，
不经过程序员定义的控制器Action
```
<!-- /index.action请求，直接转发到/index.jsp页面 -->
      <bean name="/index.action" class="org.springframework.web.servlet.mvc.ParameterizableViewController">
      		<property name="viewName" value="/index.jsp"/>
      </bean>
      
      
     <!-- 注册视图解析器(view包)(框架) 
	  <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	  		<property name="prefix" value="/"/>
	  		<property name="suffix" value=".jsp"/>
	  </bean>
      -->
```
2. org.springframework.web.servlet.mvc.AbstractCommandController
能够以实体的形式，收集客户端参数
```
public class AdminAction extends AbstractCommandController{
	public AdminAction(){
		this.setCommandClass(Admin.class);
	}
	@Override
	protected ModelAndView handle(HttpServletRequest request,HttpServletResponse response, Object obj, BindException bindException)throws Exception {
		System.out.println("AdminAction::handle");
		ModelAndView modelAndView = new ModelAndView();
		Admin admin = null;
		if(obj instanceof Admin){
			admin = (Admin) obj;
		}
		modelAndView.addObject("username",admin.getUsername());
		modelAndView.addObject("gender",admin.getGender());
		modelAndView.addObject("hiredate",admin.getHiredate());
		modelAndView.setViewName("/jsp/success.jsp");
		return modelAndView;
	}
}
```
```
<!-- 请求处理类 -->
      <bean name="/add.action" class="cn.itcast.javaee.springmvc.controller.AdminAction">
      </bean>
      
      <!-- 映射器 -->
	  <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping">
	  </bean>
```

spring-mvc中json格式对象数据传输
```
	@RequestMapping("/doAjax")
	@ResponseBody //如果返回json格式，需要这个注解，这里用来测试环境
	public Object doAjax(Supplier supplier){
		System.out.println("---doAjax.supplier:"+supplier);
		supplier.setSupName("supName1");
		return supplier;
	}
```
在spring.xml中配置
```
<!-- 同时开启json格式的支持 -->
<mvc:annotation-driven></mvc:annotation-driven>
```
struts与json数据省份-城市-区域三级联动【Struts2 + JSON版】
action
```
/**
	 * 根据省份查询城市
	 */
	public String findCityByProvinceMethod() throws Exception{
		cityList = new ArrayList<String>();
		if("湖北".equals(province)){
			cityList.add("武汉");
			cityList.add("黄岗");
		}else if("湖南".equals(province)){
			cityList.add("岳阳");
			cityList.add("张家界");
		}else if("广东".equals(province)){
			cityList.add("韶关");
			cityList.add("东莞");
		}
		return "ok";
	}
    /**
	 * 根据城市查询区域
	 */
	public String findAreaByCityMethod() throws Exception{
		areaList = new ArrayList<String>();
		if("武汉".equals(city)){
			areaList.add("AA");
			areaList.add("BB");
		}else if("黄岗".equals(city)){
			areaList.add("CC");
			areaList.add("DD");
		}else if("岳阳".equals(city)){
			areaList.add("EE");
			areaList.add("FF");
		}else if("张家界".equals(city)){
			areaList.add("GG");
			areaList.add("HH");
		}else if("韶关".equals(city)){
			areaList.add("II");
			areaList.add("JJ");
		}else if("东莞".equals(city)){
			areaList.add("KK");
			areaList.add("LL");
		}
		return "ok";
	}
private List<String> cityList;
	private List<String> areaList;
	public List<String> getCityList() {//json与struts结合使用需要提供get方法
		return cityList;
	}
	public List<String> getAreaList() {
		return areaList;
	}
```
struts.xml
```
<package name="provinceCityAreaPackage" extends="json-default" namespace="/">
	   	<action 
			name="findCityByProvinceRequest" 
			class="cn.itcast.javaee.js.provincecityarea.ProvinceCityAreaAction" 
			method="findCityByProvinceMethod">
	   		<result name="ok" type="json">
	   		</result>
	   	</action>
   </package>
```
导包：struts2-json-plugin-2.3.1.1.jar

javabean2json
使用第三方工具，将JavaBean对象/List或Set或Map对象转成JSON
    准备导入第三方jar包：
    》commons-beanutils-1.7.0.jar
    》commons-collections-3.1.jar
    》commons-lang-2.5.jar
》commons-logging-1.1.1.jar
》ezmorph-1.0.3.jar
》json-lib-2.1-jdk15.jar		
   （1）JavaBean----->JSON
	    》JSONArray jsonArray = JSONArray.fromObject(city);
	    》String jsonJAVA = jsonArray.toString();
   （2）List<JavaBean>----->JSON 
        》JSONArray jsonArray = JSONArray.fromObject(cityList);
	    》String jsonJAVA = jsonArray.toString();
   （3）List<String>----->JSON 
        》JSONArray jsonArray = JSONArray.fromObject(stringList);
	    》String jsonJAVA = jsonArray.toString(); 
   （4）Set<JavaBean>----->JSON 
        》JSONArray jsonArray = JSONArray.fromObject(citySet);
	    》String jsonJAVA = jsonArray.toString();
   （5）Map<String,Object>----->JSON 
        》JSONArray jsonArray = JSONArray.fromObject(map);
	    》String jsonJAVA = jsonArray.toString();
        最后一个例子切记，将来jQuery-EasyUI-DataGrid组件时我们还要用到
        将来，在企业中，就算脱离struts2的环境，也能用第三方工具，将Java类型转成JSON文本

### 介绍一下Spring的事务管理
事务管理对于企业应用而言至关重要。它保证了用户的每一次操作都是可靠的，即便出现了异常的访问情况，也不至于破坏后台数据的完整性。就像银行的自助取款机，通常都能正常为客户服务，但是也难免遇到操作过程中机器突然出故障的情况，此时，事务就必须确保出故障前对账户的操作不生效，就像用户刚才完全没有使用过取款机一样，以保证用户和银行的利益都不受损失。
编程式事务控制
	比较灵活，但开发起来比较繁琐： 每次都要开启、提交、回滚
声明式事务控制
	spring声明式事务管理，核心实现就是基于Aop

### springmvc与struts2的区别
1）springmvc的入口是一个servlet，即前端控制器，例如：*.action
   struts2入口是一个filter过虑器，即前端过滤器，例如：/*
2）springmvc是基于方法开发，传递参数是通过方法形参，可以设计为单例
   struts2是基于类开发，传递参数是通过类的属性，只能设计为多例
3）springmvc通过参数解析器是将request对象内容进行解析成方法形参，将响应数据和页面封装成
   	ModelAndView对象，最后又将模型数据通过request对象传输到页面
   struts采用值栈存储请求和响应的数据，通过OGNL存取数据

### Spring的安全（线程安全？）问题？

Spring框架里的bean，或者说组件，获取实例的时候都是默认的单例模式，这是在多线程开发的时候要尤其注意的地方
Spring利用ThreadLocal解决线程安全问题

我们知道在一般情况下，只有无状态的Bean才可以在多线程环境下共享，在Spring中，绝大部分Bean都可以声明为singleton作用域。就是因为Spring对一些Bean（如RequestContextHolder、TransactionSynchronizationManager、LocaleContextHolder）中非线程安全的“状态性对象”采用ThreadLocal进行封装，让它们也成为线程安全的“状态性对象”，因为有状态的Bean就能够以singleton方式在多线程中正常工作了。

### xml配置与注解的优缺点
xml配置文件方式优点：
1、降低耦合，使容易扩展。
2、对象之间的关系一目了然。
3、xml配置文件比注解功能齐全。
xml配置文件方式缺点：
1、配置文件配置工作量相对注解要大。
注解方式优点：

1、在class文件中，可以降低维护成本
2、提高开发效率。

注解方式缺点：
1、如果对annotation进行修改，需要重新编译整个工程。
2、业务类之间的关系不如XML配置那样一目了然。
3、程序中过多的annotation，对于代码的简洁度有一定影响。
4、annotation貌似功能没有xml配置文件齐全

### mybatis工作流程

```
Reader reader = Resources.getResourceAsReader("mybatis.xml");//configraution.config也可以
```

1）通过Reader对象读取src目录下的mybatis.xml配置文件(该文本的位置和名字可任意)
2）通过SqlSessionFactoryBuilder对象创建SqlSessionFactory对象

```
Reader reader = Resources.getResourceAsReader("mybatis.xml");//configraution.config也可以
			sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
```

3）从当前线程中获取SqlSession对象
4）事务开始，在mybatis中默认
5）通过SqlSession对象读取StudentMapper.xml映射文件中的操作编号，从而读取sql语句
6）事务提交，必写
7）关闭SqlSession对象，并且分开当前线程与SqlSession对象，让GC尽早回收

scm32在事务管理中注入了datasource
优点：
1. 配置generatorConfig.xml可以逆向生成entity实体文件,和相应的entityMapper.xml文件
2. 易于上手和掌握。
3. sql写在xml里，便于统一管理和优化。
4. 解除sql与程序代码的耦合。
5. 提供映射标签，支持对象与数据库的orm字段关系映射
6. 提供对象关系映射标签，支持对象关系组建维护
7. 提供xml标签，支持编写动态sql。

scm32项目的application配置
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:context="http://www.springframework.org/schema/context"
	xmlns:p="http://www.springframework.org/schema/p"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
	http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
	http://www.springframework.org/schema/context 
	http://www.springframework.org/schema/context/spring-context-3.2.xsd
	http://www.springframework.org/schema/tx 
	http://www.springframework.org/schema/tx/spring-tx-3.2.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-3.2.xsd">

	<!-- 配置数据源，记得去掉myBatis-config.xml的数据源相关配置 -->
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="com.mysql.jdbc.Driver" />
		<property name="jdbcUrl"
			value="jdbc:mysql://localhost:3306/scm?useUnicode=true&amp;characterEncoding=UTF-8" />
		<property name="user" value="root" />
		<property name="password" value="root" />
	</bean>
	<!-- 配置session工厂 -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource" />
		<property name="configLocation" value="classpath:myBatis-config.xml" />
		<!--配置扫描式加载SQL映射文件,记得去掉mybatis-config配置-->
		<property name="mapperLocations" value="classpath:cn/itcast/scm/dao/*.xml"/>
	</bean>
	<!-- 配置事务管理器,管理数据源事务处理 -->
	<bean id="transactionManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource" />
	</bean>
	<!-- 配置事务通知 -->
	<tx:advice id="advice" transaction-manager="transactionManager">
		<tx:attributes>
			<!-- 默认只处理运行时异常，可加rollback-for="Exception/Throwable"等处理所有异常或包括错误 -->
			<tx:method name="insert*" propagation="REQUIRED"
				rollback-for="Exception" />
			<tx:method name="update*" propagation="REQUIRED"
				rollback-for="Exception" />
			<tx:method name="delete*" propagation="REQUIRED"
				rollback-for="Exception" />
			<tx:method name="*" propagation="SUPPORTS" />
		</tx:attributes>
	</tx:advice>
	<!-- 配置切面织入的范围,后边要把事务边界定在service层 -->
	<aop:config>
		<aop:advisor advice-ref="advice"
			pointcut="execution(* cn.itcast.scm.service.impl.*.*(..))" />
	</aop:config>
	<!-- 配置SessionTemplate，已封装了繁琐的数据操作 -->
	<bean id="sqlSessionTemplate" class="org.mybatis.spring.SqlSessionTemplate">
		<constructor-arg name="sqlSessionFactory" ref="sqlSessionFactory" />
	</bean>
	<!-- <context:component-scan base-package="*" /> -->
	<!-- 自动扫描组件，要把controller去除，他们是在spring-mvc.xml中配置，如果不去除会影响事务管理。 -->
	<context:component-scan base-package="cn.itcast">
		<context:exclude-filter type="annotation"
			expression="org.springframework.stereotype.Controller" />
	</context:component-scan>
	<!-- 配置 转换器，对于在basePackage设置的包（包括子包）下的接口类，
	如果接口类的全类名在Mapper.xml文件中和定义过命名空间一致，
	 将被转换成spring的BEAN，在调用 
		的地方通过@Autowired方式将可以注入接口实例 -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	<!-- 扫描cn.itcast.scm.dao实体映射文件。并为每一个实体映射配置文件注入sqlSessionFactory对象，
		实体映射配置文件就可以转化成javabean对象也就是dao实例对象，
		而且通过serviceImpl中@Autowired来位serviceImpl注入dao实体对象 -->
		<property name="sqlSessionFactory" ref="sqlSessionFactory" />
		<property name="basePackage" value="cn.itcast.scm.dao" />
	</bean>
</beans>
```

缺点：
1. sql工作量很大，尤其是字段多、关联表多时，更是如此。
2. sql依赖于数据库，导致数据库移植性差。
3. 由于xml里标签id必须唯一，导致DAO中方法不支持方法重载。
4. 字段映射标签和对象关系映射标签仅仅是对映射关系的描述，具体实现仍然依赖于sql。（比如配置了一对多Collection标签，如果sql里没有join子表或查询子表的话，查询后返回的对象是不具备对象关系的，即Collection的对象为null）
5. DAO层过于简单，对象组装的工作量较大。
6.  不支持级联更新、级联删除。
7. 编写动态sql时,不方便调试，尤其逻辑复杂时。
8 提供的写动态sql的xml标签功能简单（连struts都比不上），编写动态sql仍然受限，且可读性低。
9. 若不查询主键字段，容易造成查询出的对象有“覆盖”现象。
10. 参数的数据类型支持不完善。（如参数为Date类型时，容易报没有get、set方法，需在参数上加@param）
11. 多参数时，使用不方便，功能不够强大。（目前支持的方法有map、对象、注解@param以及默认采用012索引位的方式）
12. 缓存使用不当，容易产生脏数据。
mybatis配置文件
mybatis.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC"/>
			<dataSource type="POOLED">
				<property name="driver" value="com.mysql.jdbc.Driver"/>	
				<property name="url" value="jdbc:mysql://127.0.0.1:3306/mybatis"/>	
				<property name="username" value="root"/>
				<property name="password" value="root"/>
			</dataSource>
		</environment>
	</environments>
	<mappers>
		<mapper resource="cn/volc/entity/CourseMapper.xml"/>
		<mapper resource="cn/volc/entity/StudentMapper.xml"/>
	</mappers>
</configuration>
CourseMapper.xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="courseNamespace">
	<resultMap type="cn.volc.entity.Course" id="courseMap">
		<id property="id" column="cid" />
		<result property="name" column="cname"/>
	</resultMap>
	<select id="findCourseByName" parameterType="string" resultMap="courseMap">
		select c.cid,c.cname
		from students s,middles m,courses c
		where s.sid = m.sid 
		and m.cid = c.cid
		and s.sname = #{name}
	</select>
</mapper>
```
StudentMapper.xml
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="studentNamespace">
	<resultMap type="cn.volc.entity.Student" id="studentMap">
		<id property="id" column="sid" />
		<result property="name" column="sname"/>
	</resultMap>
	<select id="findStudentByName" parameterType="string" resultMap="studentMap">
		select s.sid,s.sname
		from students s,middles m,courses c
		where s.sid = m.sid 
		and m.cid = c.cid
		and c.cname = #{name}
	</select>
</mapper>
```

### 请你谈谈SSH整合
SSH：
Struts（表示层）+Spring（业务层）+Hibernate（持久层）
Struts：
Struts是一个表示层框架，主要作用是界面展示，接收请求，分发请求。
在MVC框架中，Struts属于VC层次，负责界面表现，负责MVC关系的分发。（View：沿用JSP，HTTP，Form，Tag，Resourse ；Controller：ActionServlet，struts-config.xml，Action）
Hibernate：
Hibernate是一个持久层框架，它只负责与关系数据库的操作。
Spring：
Spring是一个业务层框架，是一个整合的框架，能够很好地黏合表示层与持久层。



### webservice

#### 网络七层协议

OSI是一个开放性的通信系统互连参考模型，他是一个定义的非常好的协议规范。OSI模型有7层结构，每层都可以有几个子层。 OSI的7层从上到下分别是 7 应用层 6 表示层 5 会话层 4 传输层 3 网络层 2 数据链路层 1 物理层 其中高层，即7、6、5、4层定义了应用程序的功能，下面3层，即3、2、1层主要面向通过网络的端到端的数据流。
#### 为什么要分层
为了为了简化网络设计的复杂性，通信协议采用分层的结构，各层协议之间既相互独立又相互高效的协调工作。对于复杂的通信协议，其结构应该是采用层次的。分层的协议可以带来很多便利：
分层的好处有：
一 灵活性好：当任何一层发生变化时，只要层间接口关系保持不变，则在这层以上或以下各层均不受影响。此外，对某一层提供的服务还可进行修改。当某层提供的服务不再需要时，甚至可以将这层取消，更容易管理。

二各层之间是独立的：在各层间标准化接口，允许不同的产品只提供各层功能的一部分，某一层不需要知道它的下一层是如何实现的，而仅仅需要知道该层通过层间的接口所提供的服务。由于每一层只实现一种相对独立的功能，所以比较容易实现！

Socket就是为网络服务提供的一种机制。
数据在两个Socket间通过IO传输。

OSI的七层模型  ： 物理层、数据链路层、网络层、传输层、表示层、会话层、应用层
     - Socket访问  ： Socket属于传输层，它是对Tcp/ip协议的实现，包含TCP/UDP,它是所有通信协议
                       的基础，Http协议需要Socket支持，以Socket作为基础
socket特点：
	  1. 开启端口，该通信是 长连接的通信 ，很容易被防火墙拦截，可以通过心跳机制
	               来实现 ，开发难度片段
	  2. 传输的数据一般是字符串 ，可读性不强
	            lj|16|1|60|up  
          3. socket端口不便于推广 
	     http:17.23.23.2:2345   www.jd.com   www.360buy.com
	  4. 性能相对于其他的通信协议是最优的
     - Http协议访问 ：属于应用层的协议，对Socket进行了封装
          1. 跨平台 
	  2. 传数据不够友好 ： 
	     get请求： http://127.0.0.1:8888?username=lj&pwd=1234
	  3. 对第三方应用提供的服务，希望对外暴露服务接口

http：属于应用层的协议，对Socket进行了封装
          1. 跨平台 
	  2. 传数据不够友好 ： 
	     get请求： http://127.0.0.1:8888?username=lj&pwd=1234
	  3. 对第三方应用提供的服务，希望对外暴露服务接口


     问题：
       1. 数据封装不够友好 ：可以用xml封装数据 
       2. 希望给第三方应用提供web方式的服务  （http + xml） = web  Service

### WebService概念介绍
1.Web Service
能使得运行在不同机器上的不同应用无须借助附加的、专门的第三方软件或硬件， 就可相互交换数据或集成。依据Web Service规范实施的应用之间， 无论它们所使用的语言、 平台或内部协议是什么， 都可以相互交换数据
通俗的讲，Web Service就是一个部署在Web服务器上的一个应用，它向外界暴露出一个能够通过Web进行调用的API。这就是说，你能够用编程的方法通过Web来调用这个应用程序。我们把调用这个Web Service 的应用程序叫做客户端,发布这个web服务的应用程序器称为Web Service服务器
2.webservice的规则
     - xml 
	 - soap ：（simple object access 协议） ：简单对象访问协议，比http协议方法速度慢
	 - wsdl ： webservice描述语言 ，它也是xml实现的

通过get或post调用第三方发的webservice服务

```
import java.io.ByteArrayOutputStream;
import java.io.FileInputStream;
import java.io.InputStream;
import java.net.HttpURLConnection;
import java.net.URL;
import org.apache.commons.httpclient.HttpClient;
import org.apache.commons.httpclient.methods.PostMethod;

/**调用第三方的webservice服务 ，获取电话号码信息
 * 
 * @author 李俊  2015年5月16日
 */
public class MobileCodeService {
	//1. http-get方式访问webservice
	public void get(String mobileCode ,String userID ) throws Exception{
		/*
		 * HTTP请求由三部分组成：请求行，请求头，请求正文
		 * 	请求行：请求方法 url/http版本
		 * 	url：
		 * 		资源路径路径：/WebServices/MobileCodeWS.asmx/getMobileCodeInfo?mobileCode=string&userID=string
		 *  	主机ip: ws.webxml.com.cn(主机的IP地址)
		 */
		URL url=new URL("http://webservice.webxml.com.cn/WebServices/MobileCodeWS.asmx/getMobileCodeInfo?mobileCode="+mobileCode+
				"&userID="+userID);
		//开启连接
		HttpURLConnection conn=(HttpURLConnection) url.openConnection();
		//延迟时间
		conn.setConnectTimeout(5000);
		//请求方法
		conn.setRequestMethod("GET");
		//响应码
		if(conn.getResponseCode()==HttpURLConnection.HTTP_OK){ //获取并判断相应结果码是否为200，成功响应了以后才能获取返回的数据
			InputStream is=conn.getInputStream();
			//写出到内存的流 ，将流对象写出到内存中  
			ByteArrayOutputStream boas=new ByteArrayOutputStream();
			byte[] buffer=new byte[1024];
			int len=-1;
			while((len=is.read(buffer))!=-1){//输入流开始读到buffer中，每一次读取判断并判断是否读到了内容
				boas.write(buffer, 0, len);
			}
		    System.out.println("GET请求获取的数据:"+boas.toString());	
		    boas.close();
		    is.close();
		}
	}
	
	//2.Post请求 ：通过Http-Client 框架来模拟实现 Http请求
	public void post(String mobileCode ,String userID) throws Exception{
		/**HttpClient访问网络的实现步骤：
		 *  1. 准备一个请求客户端:浏览器 
		 *  2. 准备请求方式： GET 、POST
		 *  3. 设置要传递的参数
		 *  4.执行请求
		 *  5. 获取结果
		 */
		HttpClient client=new HttpClient();
		PostMethod postMethod=new PostMethod("http://webservice.webxml.com.cn/WebServices/MobileCodeWS.asmx/getMobileCodeInfo");
		//3.设置请求参数
		postMethod.setParameter("mobileCode", mobileCode);
		postMethod.setParameter("userID", userID);
		//4.执行请求 ,结果码
		int code=client.executeMethod(postMethod);
		//5. 获取结果
		String result=postMethod.getResponseBodyAsString();
		System.out.println("Post请求的结果："+result);
	}
	//2.Post请求 ：通过Http-Client 框架来模拟实现 Http请求
	public void soap() throws Exception{

		HttpClient client=new HttpClient();
		PostMethod postMethod=new PostMethod("http://webservice.webxml.com.cn/WebServices/MobileCodeWS.asmx");
		//3.设置请求参数
          postMethod.setRequestBody(new FileInputStream("c:/soap.xml")); 
          //修改请求的头部
          postMethod.setRequestHeader("Content-Type", "text/xml; charset=utf-8");
		//4.执行请求 ,结果码
		int code=client.executeMethod(postMethod);
		System.out.println("结果码:"+code);
		//5. 获取结果
		String result=postMethod.getResponseBodyAsString();
		System.out.println("Post请求的结果："+result);
	}

	public static void main(String[] args) throws Exception{
		MobileCodeService ws=new MobileCodeService();
		ws.get("15626217879", "");
		ws.post("15626217879", "");
//	    ws.soap();

	}

}
```

使用Wsimpot命令生成webservice的本地代理，通过本地代理访问webservice
命令语句：来解析WSDL文件其实就是xml文件
	wsimport [opations] <wsdl_uri>
	     - wsdl_uri:wsdl 的统一资源标识符
	     - d  ：指定要输出的文件的位置
	     - s  ：表示要解析java的源码 ，默认解析出的是class字节码 
	     - p  ： 指定输出的包名
wsimport –s ./ -p cn.it.ws.c http://ws.webxml.com.cn/WebServices/MobileCodeWS.asmx?WSDL
./:代表存放当前路径
执行语句会生成java源码和class字节码文件
使用java代码中的代理就可以调用web service中的工能。不能自己改变java代码，相当于不能改变代理


### 使用CXF框架，发布webservice服务，并使用客户端远程访问webservice

  1. CXF介绍 ：soa的框架
    * cxf 是 Celtrix （ESB框架）和 XFire（webserivice） 合并而成，并且捐给了apache  
    * CxF的核心是org.apache.cxf.Bus(总线)，类似于Spring的 ApplicationContext
    * CXF默认是依赖于Spring的
    * Apache CXF 发行包中的jar，如果全部放到lib中，需要 JDK1.6 及以上，否则会报JAX-WS版本不一致的问题
    * CXF 内置了Jetty服务器 ，它是servlet容器，好比tomcat
CXF框架概念介绍
Apache CXF 是一个开源的 Services 框架，CXF 帮助您来构建和开发 Services 这些 Services 可以支持多种协议，比如：SOAP、POST/HTTP、RESTful HTTP CXF 大大简化了 Service可以天然地和 Spring 进行无缝集成。

  2.CXF特点

    1. 与Spring、Servlet做了无缝对接，cxf框架里面集成了Servlet容器Jetty 
    2. 支持注解的方式来发布webservice
    3. 能够显示一个webservice的服务列表
    4. 能够添加拦截器：输入拦截器、输出拦截器 ：
         输入日志信息拦截器、输出日志拦截器、用户权限认证的拦截器

### Spring+CXF整合来管理webservice
    实现步骤：
      1. 添加cxf.jar 包（集成了Spring.jar、servlet.jar ）,spring.jar包 ，servlet.jar 包
      2. 编写业务类，通过CXF来发布webservice
         员工管理：
	   方法 ：添加员工、查询员工  
	        
      3. 添加一个CXF请求的 Servlet，用来处理webservice的请求 
           过滤的地址/ws/*
      4. 配置Spring的配置文件： applicationContext.xml ,把cxf的bean在spring配置
          
      5. 在web.xml中配置 CXF的 Servlet ， 添加spring的监听 

      6. 通过wsimport生成本地代理 ，访问webservice




### weblogic

集群步骤
1.创建多个服务器
	AdminServer(默认)
	监听地址：127.0.0.1
	服务器端口：7001
	server-1：
	监听地址127.0.0.1(不要写localhost)
	服务器端口号7002
	server-2:
	监听地址127.0.0.1
	服务器端口号7003
2.部署到项目到这两个服务器
	web项目在tomcat发不完之后，才会在部署-》安装(找到web项目所在的路径)下面才会显示web项目
	然后部署到这两个服务器上面。
3.使用D:\Oracle\Middleware\user_projects\domains\base_domain\bin下的startManagedWeblogic.cmd(启动受管服务器得命令server-1，server-2)
	C:\Users\Administrator>D:\Oracle\Middleware\user_projects\domains\base_domain\bin\startManagedWebLogic.cmd Server-1 http://127.0.0.1:7001(Server-1名称大写敏感)
   被管理服务器名 管理服务器的ip
	验证：部署，部署到两个服务器的项目，测试，看一下是否成功
4.新建一个代理服务器
	监听地址：127.0.0.1
	端口：7000
5.1创建集群
	名：Cluster-0
	多点传送
	239.192.0.0
	端口：7777
5.2添加server到集群(server必须关闭状态[cdm命令行trl+c关闭])
	放入两个server，不要放proxy
6.使用quick start进行扩展域
	get start…找到已创建的base-domain域，下一步，选择第二个jax-rpc(weblogic的访问模式)勾选受管服务器，集群和部署服务两项配置受管得服务器，下一步到向集群分发服务器(可以看默认有两个server在里面)创建http代理勾选http代理，指定用proxy代理两个server下一步到将要部署定位到集群或服务器，在cluster-0下选择那个已经部署到两个server的项目(testweblogic4t),下一步到扩展，结束。
	总结：扩展域过程中，除了配置勾选http指定定代理server，其余过程都可以默认不用勾选。
	会在D:\Oracle\Middleware\user_projects\domains\base_domain\apps\生成(OracleProxy4_Cluster-0_Proxy)
	OracleProxy4_Cluster-0_Proxy这个文件夹中封装了集群信息。(文件夹放到项目所在目录)
	修改这个文件夹中的weblogic.xml为：(访问路径)
<weblogic-web-app>
<context-root>/testweblogic4t</context-root>
</weblogic-web-app>
7.回到console，进行安装部署OracleProxy4_Cluster-0_Proxy
	服务器选择Proxy这个代理服务器，下一步，到完成
8.删除已经部署过的testweblogic4t项目重新部署到所有的集群，而不是再单个部署到每一个server中。
9.启动server-1，server-2.Proxy服务
	C:\Users\Administrator>D:\Oracle\Middleware\user_projects\domains\base_domain\bin\startManagedWebLogic.cmd Server-1 http://127.0.0.1:7001
	C:\Users\Administrator>D:\Oracle\Middleware\user_projects\domains\base_domain\bin\startManagedWebLogic.cmd Server-2 http://127.0.0.1:7001
	C:\Users\Administrator>D:\Oracle\Middleware\user_projects\domains\base_domain\bin\startManagedWebLogic.cmd Proxy http://127.0.0.1:7001
10.在console集群中的项目进行测试。

通过console部署指定的文件夹下的项目：
   这个项目必须是war包或者从tomcat安装目录下的webapps中已经部署到tomcat中的项目。
  例如console要部署weblogictest文件夹下面的项目testweblogic4t，那么这个testweblogic4t一定是tomcat部署过的。要么是war包，要么是从webapps目录下拷贝过来的。这时console中指定安装目录下才会显示这个项目testweblogic4t，可以进行部署。

项目中属性project facets java版本和web module版本与开发工具环境有关。
	开发工具环境越高版本项目中属性project facets java版本和web module版本就越高

部署正确到weblogic oracleweblogic server 11gr1(10.3.6)服务器的条件：
	项目中project facets的java版本小于等于1.6，web module小于等于2.5，跟有无jdk环境和服务器环境包无关。

index.jsp保存原因是没有导入服务器包weblogic system library或者tomcat system library。

项目是否报错原因：
	1.开发工具指定jdk环境版本小于项目中属性project facets的java版本。
		出现的原因：例如：原来开发工具环境1.8jdk，项目中属性project facets的java版本1.8，				这是降低开发工具环境版本就会报错。
		不管eclipse的开发环境jdk版本是多少，与项目中实际引入的jdk版本是无关的，不会          报错，项目中有无jdk版本和服务器环境(如：tomcat system library)无关，不会报错。
	2.代码错误。

当有项目报错时，tomcat无法部署任何项目。而weblogic是可以的。


eclipse中部署到tomcat6服务器的条件：除添加不是tomcat环境包(如：weblogic system library)情况下都可以部署。


集群的负载均衡：(使用不同浏览器测试)
	一个session访问一个资源时，代理服务器，使用server-1
	另一个session访问同一个资源时，代理服务器，会使用server-2.

集群的故障转移：
	如：
	server-2进行登录成功，jsp页面显示两个sessionid同时对应一个会话。后server-2停掉
	仍然进行其他操作时，代理proxy就会启动server-1进行继续业务操作。这是jsp页面就只剩一个活跃状态的sessionid。









### linux

#### Linux目录结构
![y](https://volc1612.gitee.io/blog/images/interview-question/y.png)
1.bin ： 存放的可执行的二进制文件
   cd  ls  su  passwd
2. boot ：存放系统的引导文件的目录
3. dev 存放设备文件的目录，linux把设置当做文件来处理
4. etc ： 存放系统的配置文件的目录
5. home ：存放所有用户文件的根目录
       ，root用户除外
6. lib ：共享库
7.usr ：好比program files 
     存放应用的安装的路径
8 opt ： 自定义存放应用程序位置
9. mnt ：临时文件系统的挂靠点

ls –l[查看本目录下所有目录的信息权限]
#### 目录结构及权限
    - 文件权限分析
      w ：可写   r： 只读  x：可执行  - ：无权限
      文件权限
       1. 字符表示法
         drwxr-xr-x(目录权限)
	  第一个字符表示文件的类型  
			d ：普通文件  
			- ：文件夹  
			c ：串口文件  
			l ：连接文件
       2-4(rwx) 字符  ： 该文件的属主用户的权限(读写执行)
	   5-7(r-x)字符 ： 与属主用户同一组的其他用户的权限(‘-‘无权限)
	   8-10(r-x)字符 ：  不同组的其他用户的权限(‘-‘无权限)
        2. 数字表示法
	   -rw-r--r-- :表示这个文件的默认权限   644
			二进制：
			1111 1111 1111 1111 1111 1111 1111 1111(32位)
			八进制：
			111  111  111  111  111  111  111  111(一位最大值为7)
			rw-代表1106
			r—代表1004
	   drwxr-xr-x :表示目录的默认权限  755  



   默认有6个命令交互通道和一个图形界面交互通道，默认进入到的是图形界面通道
     命令交互模式切换：ctrl+alt+f1---f6
     图形交互界面 ctrl+alt+f7
   1.图形界面交互模式
     - terminal： 图形界面的命令终端，它是图形界面交互通道的延伸，要依赖于图形界面
   2.命令交互模式 
      命令提示符：
      itcast@ubuntu:~$ 
        - itcast：用户名
	- ubuntu ：主机名
	- ~  ：路径 ，假如当前的路径正好是 该用户存放数据的根目录 ，则显示~
	- $ :用户的类型  $代表普通用户  #  代表 超级用户
进入桌面的命令
volc@ubuntu:~/Desktop/a/b$ cd ~/Desktop
cd ..[表示返回上一目录]
cd a[表示进入当前目录下的a文件夹]
volc@ubuntu:~$ cd Desktop[表示在volc当前目录下进入 Desktop桌面]



1.注销、关机、重启
     - 注销 ：logout ：登出 、exit 
     - 关机 ：
        shutdown - h  时间
	  - h ：关机
	  - 时间 ：
	    1. now :马上
	    2. 12.30 ：指定具体时间
	    3. 3  ：几分钟以后
	 sudo ： superuser do ：由超级用户来执行该命令
	   要配置sudo 命令 ： 授权 哪些用户能执行哪些命令
	    由超级用户配置  sudo 
	     /etc/sudoers 
	   sudo shutdown -h now    当前账号：itcast  
     - 重启 ：
        shutdown -r  时间
	  -r ：restart 

   2.linux基本命令 ：文件操作命令
    
     1. ls   ： 查看目录内容
        - l ：查看详细信息
	- a ：查看所有文件（隐藏）
        man ：manual ： 手工，帮助 ，帮助命令，好比windows help 
	 命令： man ls
     2. mkdir ：创建目录 
          Desktop：
	    - java
	      - JEE ： aa.txt  bb.txt  
	      - Android
     3. cd  切换目录
        - cd ..  记得要加空格
	- cd ./java  进入当前目录的子目录
	- cd ../xxx  进入上一级目录的子目录
	   tab ：自动补全
     4. touch  ：创建一个空白的普通文件
        touch aa.txt 
     5. echo  :把内容重定向到指定的文件中 ，有则打开，无则创建
     6. cat、more  ：查看文件内容
        - cat ：查看文件内容
	- more ：分页查看文件内容，按空格键换页
	     
     7. cp、mv、rm   
        - cp ：复制
	   cp bj.txt ./java/jee
	- mv ：剪切、重命名
	   1. 剪切 ：mv aa.txt ../android/
	   2. 重命名 ：假如剪切的文件 存放在同一个目录中 ，则是重命名
	       mv bb.txt cc.txt

	- rm ：删除文件或者文件夹
	  -f ： 假如要删除的文件不存在，也不提示
	  -i ： 删除前提示 ，默认不删除，要删除，输入y
	  -d ： 删除空白目录 
	  -r ：递归删除
     8. wc ：word count ：统计字符数
         154  233 3418 
	 - 154 ：行数
	 - 233 ：单词数
	 - 3418 ：字符数
       命令： wc bj.txt
        
     9. ln :创建连接文件
        - 默认创建的是硬连接，好比复制 ，但是两个文件会同步 
	   命令：ln ./java/android/aa.txt  aaa
	- s ：创建的是软连接
        
     10. pwd   ：查看当前目录的绝对路径 
       
     11. 管道命令 |
       命令： ls -la | wc 
     12. 重定向
        - > :覆盖模式
	命令：  echo "ww">aaa
	- >>：追加模式
	命令： echo "ww">>aaa
     13. passwd   :设置密码  ，ubuntu默认 root账号是没有开启 ，只要设置root密码即可开启
         sudo passwd root

     14. su  切换目录
         su root 
	 root用户切换到其他账号不需要密码

   3.linux 系统命令
     1.stat   ：查看文件的详细信息
       stat bj.txt
     2.who与whoami
       who : 查看在线的用户
       whoami ：查看当前自己的用户
     3.hostname ： 显示主机名
       hostname
     4.uname  ：显示系统信息
        -a ：显示完整的系统信息
        
     5.top ：显示当前耗时的进行的信息 ，每3秒刷新一次 
         cltr+c 中断 
     6.ps ：显示当前进程的快照 
       - axu
     7.du ：显示文件的大小信息 
     8.df ：磁盘使用情况  disk free
     9.ifconfig ：查看或者配置网卡信息 ，好比windows 的ipconfig
        ipv4：32位  2-32次方     ipv6  128位     是 ipv4  2-96次方倍数
	设置虚拟机ip地址：
	  1. 设置vmware的连接方式
	     - 共享宿主机的ip地址，在网上邻居找不到
	     - 桥接方式，需要单独设置ip，可以在网上邻居查找
	  2. 图形界面设置ip地址 
	      edit connection --》ipv4--》manual（手工设置）--》add  （ip地址，子网掩码）
          3.命令方式设置ip地址
	     静态设置ip：
	     sudo ifconfig eth0 192.168.15.122 netmask 255.255.255.0 
     10.ping  测试与目标主机连接情况
     11.clear  ： 清除屏幕  windows： cls  
     12.man  ：帮助命令
         man 命令
     13.kill  ：杀死进程
        kill  pid 
     14.netstat  ：网络连接详细信息
       
     15. useradd 
        - 查看用户信息
	   sudo cat /etc/passwd
	 itcast:x:1000:1000:UbuntuA,,,:/home/itcast:/bin/bash
	  - itcast:用户名
	  - x ：密码 ：已经加密 ，密码存放在  /etc/shadow 
	  - 1000: 账号id ，userId
	  - 1000： 组id ，group id
          - UbuntuA,,,  ：账号描述
          - /home/itcast ：该账号存放文件的默认位置  ~
	  - /bin/bash：该用户的shell脚本的解析方式  ，sh 、bash、rbash
        - 创建用户 
	  sudo useradd lijun -d  /home/lijun   -s /bin/bash 
	   -d :指定该用户的home 路径 
	   - s ：该用户的shell解析方式
	  步骤：
	    1. 创建 /home/lijun 目录
	    2. 执行 useradd 命令
	    3. 用 passwd 设置密码
	    4. su 切换用户

### Tomcat、Weblogic的区别

什么是weblogic
WebLogic是美国Oracle公司出品的一个application server确切的说是一个基于JAVAEE架构的中间件，BEA WebLogic是用于开发、集成、部署和管理大型分布式Web应用、网络应用和数据库应用的Java应用服务器。
| Weblogic特点 | Tomcat特点 |

| WLS全面支持J2EE的标准规范和其他标准规范,Web Service, SSL, xml,EJB等 | Tomcat 为WEB容器，只支持部分J2EE标准, 应用局限性强,不支持EJB |
| 完善的售后支持 | 没有售后支持, 看社区与论坛服务器出现的问题 |
| 集群机制, 支持分布式的应用 | 需要结合第三方插件/应用 |
| Web控制台进行组件、JDBC、管理和配置 | 差 |
| 较好的支持热部署（开发模式下）实现某个单一类的热部署，让系统无需重启就完成某个类的更新 | 差 |
| 需要费用 | 开源免费 |


### Lucene

Lucene是apache软件基金会发布的一个开放源代码的全文检索引擎工具包，Lucene是根据关健字来搜索的文本搜索工具，只能在某个网站内部搜索文本内容，不能跨网站搜索，Lucece不能用在互联网搜索（即像百度那样），只能用在网站内部的文本搜索。
lucene索引库的结构
词汇表：以单个汉字进行分词建立的索引库
		“是”词汇的值有三个引用012分别作为三个数组，每个数组的值分别指向三个document对象
原始记录表：封装了document对象，document对象存放具体Article实例对象的属性值

![z](https://volc1612.gitee.io/blog/images/interview-question/z.png)
Lucene程序宏观结构：

![a1](https://volc1612.gitee.io/blog/images/interview-question/a1.png)

词汇表单个词汇的含义
![a2](https://volc1612.gitee.io/blog/images/interview-question/a2.png) 

创建索引库：
1）	创建JavaBean对象
2）	创建Docment对象
3）	将JavaBean对象所有的属性值，均放到Document对象中去，属性名可以和JavaBean相同或不同
4）	创建IndexWriter对象
5）	将Document对象通过IndexWriter对象写入索引库中
6）	关闭IndexWriter对象

根据关键字查询索引库中的内容：
1）	创建IndexSearcher对象
2）	创建QueryParser对象
3）	创建Query对象来封装关键字
4）	用IndexSearcher对象去索引库中查询符合条件的前100条记录，不足100条记录的以实际为准
5）	获取符合条件的编号
6）	用indexSearcher对象去索引库中查询编号对应的Document对象
7）	将Document对象中的所有属性取出，再封装回JavaBean对象中去，并加入到集合中保存，以备将之用
导包lucene
  lucene-core-3.0.2.jar【Lucene核心】
  lucene-analyzers-3.0.2.jar【分词器】
  lucene-highlighter-3.0.2.jar【Lucene会将搜索出来的字，高亮显示，提示用户】
  lucene-memory-3.0.2.jar【索引库优化策略】
代码示例：

```java
import java.io.File;
import java.util.ArrayList;
import java.util.List;
import org.apache.lucene.analysis.Analyzer;
import org.apache.lucene.analysis.standard.StandardAnalyzer;
import org.apache.lucene.document.Document;
import org.apache.lucene.document.Field;
import org.apache.lucene.document.Field.Index;
import org.apache.lucene.document.Field.Store;
import org.apache.lucene.index.IndexWriter;
import org.apache.lucene.index.IndexWriter.MaxFieldLength;
import org.apache.lucene.queryParser.QueryParser;
import org.apache.lucene.search.IndexSearcher;
import org.apache.lucene.search.Query;
import org.apache.lucene.search.ScoreDoc;
import org.apache.lucene.search.TopDocs;
import org.apache.lucene.store.Directory;
import org.apache.lucene.store.FSDirectory;
import org.apache.lucene.util.Version;
import org.junit.Test;
import cn.volc.entity.Article;

public class FirstLucene {
	
	/*
	 * 创建lucene索引库，包括，词汇表和原始数据(Article类型)
	 * 	分为4个步骤
	 * 	1.创建原始记录的数据。
	 *  2.创建索引库是通过document对象创建的，先创建一个document，用来封装原始记录数据。
	 *  3.将document对象写入到lucene索引库中。那么Lucene索引库中包含，词汇表和document对象(document对象封装了原始数据article对象)。
	 *  4.关闭索引字符流对象。
	 */
	@Test
	public void createIndexDB() throws Exception{
		//1.创建原始记录的数据。
		Article article = new Article(1,"原始数据头标题title","原始数据的内容content");
		//2.创建索引库是通过document对象创建的，先创建一个document，用来封装原始记录数据，
		Document document = new Document();
		//2.2将article对象绑定到document中。document存放的字段field,field用来封装原始记录的信息
		//每一个field包括三个参数
		/*
		 * 参数一：document的属性名"id"，项目中提倡与article属性名称相同
		 * 参数二：document对象中属性"id"的值为article的属性id，与article对象中相同
		 * 参数三：是否将id属性值存入到有原始记录转存入到词汇表
		 * 			项目中非id值都提倡放入词汇表中
		 * 参数四：是否将id属性值进行分词算法拆分
		 * 			项目中非id属性都提倡进行拆分
		 */
		document.add(new Field("id",article.getId().toString(),Store.YES,Index.ANALYZED));
		document.add(new Field("title",article.getTitle(),Store.YES,Index.ANALYZED));
		document.add(new Field("content",article.getContent(),Store.YES,Index.ANALYZED));
		//3.将document对象写入到lucene索引库中，那么Lucene索引库中包含，词汇表和document对象(document对象封装了原始数据article对象)
		Directory directory = FSDirectory.open(new File("E:/eworkspace/lucene-01/LuceneDBDBDBDBDBDBDBDBDB"));
		Analyzer analyzer = new StandardAnalyzer(Version.LUCENE_30);
		MaxFieldLength maxFieldLength = MaxFieldLength.LIMITED;
		/*
		 * 参数一：lucene索引库最终对应硬盘中的目录,"E:/eworkspace/lucene-01/LuceneDBDBDBDBDBDBDBDBDB",如果没有将自动创建
		 * 参数二：采用什么策略将文本拆分，查看策略就是它的实现类(ctrl+T选中IndexWriter查看它的实现类)
		 * 参数三：最多将文本拆分成多少个词汇，LIMITED表示1w个，也就是field的值取前1万个(拆分策略是一个汉字作为一个词汇)作为词汇。
		 * 			不足1万个以实际为准，例如值有5个汉字，就把这5个汉字作为词汇
		 */
		//3.1创建一个索引对象，(指定索引存放硬盘的目录，文本拆分策略，最多拆分词汇数目)
		IndexWriter indexWriter = new IndexWriter(directory,analyzer,maxFieldLength);
		//3.2真正将document对象写入到lucene索引库中
		indexWriter.addDocument(document);
		//4.关闭索引字符流对象
		indexWriter.close();
	}
	
	/* 
	 * 根据关键字从索引库中查询符合条件的content
	 */
	@Test
	public void findIndexDB() throws Exception{
		//1.创建集合用于接收数据对象
		List<Article> articleList = new ArrayList<Article>();
		//指定关键字
		String keywords = "内"; //这样两个汉字搜索是可以的，lucene索引关键字不能为空，为空会报错，含有空格也不行		
        Directory directory = FSDirectory.open(new File("E:/eworkspace/lucene-01/LuceneDBDBDBDBDBDBDBDBDB"));
		Version version = Version.LUCENE_30;
		//解析器类型(版本号)
		Analyzer analyzer = new StandardAnalyzer(version);
		//创建查询解析器或查询策略对象(指定策略版本，搜索document的哪个字段，解析策略)
		/*
		 * 参数一：分词器版本，提倡使用该jar包中的最高版本
		 * 参数二：针对document哪个字段进行搜索(根据"content"搜索)
		 * 参数三：指定标准解析策略
		 */
		QueryParser queryParser = new QueryParser(version,"content",analyzer);
		//通过查询解析器解析一个查询对象(查询对象带有关键字)用于查询。
		Query query = queryParser.parse(keywords);
		//2.创建搜索字符流对象IndexSearcher，指定搜索索引库在硬盘的具体目录
		IndexSearcher indexSearcher = new IndexSearcher(directory);
		int MAN_RECORD = 10;
		//3.通过搜索字符流对象indexSearcher进行搜索(根据关键字，去索引库中的词汇表中进行搜索的)
		/*
		 * 参数一：表示封装关键字的查询对象
		 * 参数二：如果关键字搜索出来的内容较多只取前MAN_RECORD个内容
		 * 			不足MAN_RECORD以实际为准
		 */
		//3.通过搜索字符流对象进行搜索(指定查询对象 ，查询记录数)，返回词汇表记录
		TopDocs topDocs = indexSearcher.search(query, MAN_RECORD);
		//4.获取词汇表的数组，然后遍历词汇表中符合条件的编号，scoreDocs代表三个[0],[1],[2]数组集合，也就是符合条件的document的个数		
           for(int i=0;i<topDocs.scoreDocs.length;i++){
			//topDocs代表词汇表中左侧的多个数[0],[1],[2]，获取每一个数组
			ScoreDoc scoreDoc = topDocs.scoreDocs[i];//
			//获取每一个数组的内容编号，如0
			int no = scoreDoc.doc;
			//5.根据编号去索引库中的原始记录表中查询对应的document对象
			Document document = indexSearcher.doc(no);
			//6.获取document对象的属性值
			String id = document.get("id");
			String title = document.get("title");
			String content = document.get("content");
			//7.封装到article对象中
			Article article = new Article(Integer.parseInt(id),title,content);
			//8.将article对象保存到list集合中
			articleList.add(article);
		}
		//9.遍历结果集
		for(Article article : articleList){
	System.out.println(article.getId()+":"+article.getTitle()+":"+article.getContent());
		}
	}
	
}
```

优化LuceneUtil.java工具类

```java
import java.io.File;
import java.lang.reflect.Method;
import org.apache.commons.beanutils.BeanUtils;
import org.apache.lucene.analysis.Analyzer;
import org.apache.lucene.analysis.standard.StandardAnalyzer;
import org.apache.lucene.document.Document;
import org.apache.lucene.document.Field;
import org.apache.lucene.document.Field.Index;
import org.apache.lucene.document.Field.Store;
import org.apache.lucene.index.IndexWriter.MaxFieldLength;
import org.apache.lucene.store.Directory;
import org.apache.lucene.store.FSDirectory;
import org.apache.lucene.util.Version;

public class LuceneUtil {
	private static Directory directory ;
	private static Analyzer analyzer ; 
	private static Version version;
	private static MaxFieldLength maxFieldLength;
	static{
		try {
			directory = FSDirectory.open(new File("E:/LuceneDBDBDBDBDBDBDBDBDB"));
			version = Version.LUCENE_30;
			analyzer = new StandardAnalyzer(version);
			maxFieldLength = MaxFieldLength.LIMITED;
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}
	public static Directory getDirectory() {
		return directory;
	}
	public static Analyzer getAnalyzer() {
		return analyzer;
	}
	public static Version getVersion() {
		return version;
	}
	public static MaxFieldLength getMaxFieldLength() {
		return maxFieldLength;
	}
	//将javabean对象转成document对象
	public static Document javabean2documemt(Object obj) throws Exception{
		Document document = new Document();
		//使用传递过来的javabean对象获取器字节码对象
		Class clazz = obj.getClass();
		//通过字节码对象获取javabean对象的字段属性包括私有的属性，并反返回的是一个数组
		java.lang.reflect.Field[] reflectFields = clazz.getDeclaredFields();
		for(java.lang.reflect.Field field : reflectFields){
			//使用暴力反射javabean私有的属性，为了取出属性名称
			field.setAccessible(true);
			String fieldName = field.getName();
			//获取属性的的第一个字母转成大写
			String init = fieldName.substring(0,1).toUpperCase();
			//拼接属性的getXxx()名
			String methodName = "get" + init + fieldName.substring(1);
			//获取方法[getMethod与getDeclareMethod的区别在于后者可以返回私有方法]
			Method method = clazz.getDeclaredMethod(methodName,null);
			//执行方法invoke(返回值类型，方法所需的参数)
			String returnValue = method.invoke(obj,null).toString();
			//javabean属性与document的属性完全相同
			document.add(new Field(fieldName,returnValue,Store.YES,Index.ANALYZED));
		}
		return document;
	}
	//将document转成javabean
	public static Object document2javabean(Document document,Class clazz) throws Exception{
		Object obj = clazz.newInstance();
		java.lang.reflect.Field[] reflectFields = clazz.getDeclaredFields();
		for(java.lang.reflect.Field field : reflectFields){
			field.setAccessible(true);
			String fieldName = field.getName();
			String fieldValue = document.get(fieldName);
			//通过BeanUtils封装javabean，需要导入[commons-beanutils-1.7.0.jar(依赖后者日志包)和commons-logging-1.1.1.jar]
			BeanUtils.setProperty(obj,fieldName,fieldValue);
		}	
		return obj;
	}
	//私有构造目的不让其他类创建LuceneUtil对象
	private LuceneUtil() {
	}
	
}
```

### 索引库的CREATE UPDATE RETRIEVE(取回) DELETE

```
//修改索引库
/*
  参数一：term
*/
indexWriter.updateDocument(term,document);
```


1.创建ArticleDao.java类
```java
public class ArticleDao {
	public Integer getAllObjectNum(String keywords) throws Exception{
		QueryParser queryParser = new QueryParser(LuceneUtil.getVersion(),"content",LuceneUtil.getAnalyzer());
		Query query = queryParser.parse(keywords);
		IndexSearcher indexSearcher = new IndexSearcher(LuceneUtil.getDirectory());
		TopDocs topDocs = indexSearcher.search(query,3);

		return topDocs.totalHits;
	}
	public List<Article> findAllObjectWithFY(String keywords,Integer start,Integer size) throws Exception{
		List<Article> articleList = new ArrayList<Article>();
		QueryParser queryParser = new QueryParser(LuceneUtil.getVersion(),"content",LuceneUtil.getAnalyzer());
		Query query = queryParser.parse(keywords);
		IndexSearcher indexSearcher = new IndexSearcher(LuceneUtil.getDirectory());
		TopDocs topDocs = indexSearcher.search(query,100000000);
        		/*
		 * 判断是否为最后几条，如果最后几条个数小于了一页显示的个数，即topDocs.totalHits<start+size,
		 * 那么仍然使用页大小来进行循环迭代就会出现ArrayIndexsOutOfBoundsException数组角标越界的异常
		 * 解决方法就是进行topDocs.totalHits和start+size大小的比较去最小的那个，真实的记录数
		 */
		int middle = Math.min(start+size,topDocs.totalHits);
		for(int i=start;i<middle;i++){
			ScoreDoc scoreDoc = topDocs.scoreDocs[i];
			int no = scoreDoc.doc;
			Document document = indexSearcher.doc(no);
			Article article = (Article) LuceneUtil.document2javabean(document,Article.class);
			articleList.add(article);
		}
		return articleList;
	}
}
```
步二：创建PageBean.java类
```java
public class PageBean {
	private Integer allObjectNum;
	private Integer allPageNum;
	private Integer currPageNum;
	private Integer perPageNum = 2;
	private List<Article> articleList = new ArrayList<Article>();
	public PageBean(){}
	public Integer getAllObjectNum() {
		return allObjectNum;
	}
	public void setAllObjectNum(Integer allObjectNum) {
		this.allObjectNum = allObjectNum;
		if(this.allObjectNum % this.perPageNum == 0){
			this.allPageNum = this.allObjectNum / this.perPageNum;
		}else{
			this.allPageNum = this.allObjectNum / this.perPageNum + 1;
		}
	}
	public Integer getAllPageNum() {
		return allPageNum;
	}
	public void setAllPageNum(Integer allPageNum) {
		this.allPageNum = allPageNum;
	}
	public Integer getCurrPageNum() {
		return currPageNum;
	}
	public void setCurrPageNum(Integer currPageNum) {
		this.currPageNum = currPageNum;
	}
	public Integer getPerPageNum() {
		return perPageNum;
	}
	public void setPerPageNum(Integer perPageNum) {
		this.perPageNum = perPageNum;
	}
	public List<Article> getArticleList() {
		return articleList;
	}
	public void setArticleList(List<Article> articleList) {
		this.articleList = articleList;
	}
}
```
步三：创建ArticleService.java类
```java
public class ArticleService {
	private ArticleDao articleDao = new ArticleDao();
	public PageBean fy(String keywords,Integer currPageNum) throws Exception{
		PageBean pageBean = new PageBean();
		pageBean.setCurrPageNum(currPageNum);
		Integer allObjectNum = articleDao.getAllObjectNum(keywords);
		pageBean.setAllObjectNum(allObjectNum);
		Integer size = pageBean.getPerPageNum();
		Integer start = (pageBean.getCurrPageNum()-1) * size;
		List<Article> articleList = articleDao.findAllObjectWithFY(keywords,start,size);
		pageBean.setArticleList(articleList);
		return pageBean;
	}
}
```
步四：创建ArticleServlet.java类
```java
public class ArticleServlet extends HttpServlet {
	public void doPost(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException {
		try {
			request.setCharacterEncoding("UTF-8");
			Integer currPageNum = Integer.parseInt(request.getParameter("currPageNum"));
			String keywords = request.getParameter("keywords");
			ArticleService articleService = new ArticleService();
			PageBean pageBean = articleService.fy(keywords,currPageNum);
			request.setAttribute("pageBean",pageBean);
			request.getRequestDispatcher("/list.jsp").forward(request,response);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```
第五步：导入EasyUI相关的js包的目录
 
![a3](https://volc1612.gitee.io/blog/images/interview-question/a3.png)

步六：在WebRoot目录下创建list.jsp
```
<%@ page language="java" pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
  	<link rel="stylesheet" href="themes/default/easyui.css" type="text/css"></link>
    <link rel="stylesheet" href="themes/icon.css" type="text/css"></link>
    <script type="text/javascript" src="js/jquery.min.js"></script>
    <script type="text/javascript" src="js/jquery.easyui.min.js"></script>
    <script type="text/javascript" src="locale/easyui-lang-zh_CN.js"></script>
  </head>
  <body>
  
  
  	<!-- 输入区 -->
	<form action="${pageContext.request.contextPath}/ArticleServlet?currPageNum=1" method="POST">
		输入关健字：<input type="text" name="keywords" value="传智" maxlength="4"/>
		<input type="button" value="提交"/>
	</form>
 	
 	
 	<!-- 显示区 -->
	<table border="2" align="center" width="70%">
		<tr>
			<th>编号</th>
			<th>标题</th>
			<th>内容</th>
		</tr>
		<c:forEach var="article" items="${pageBean.articleList}">
			<tr>
				<td>${article.id}</td>
				<td>${article.title}</td>
				<td>${article.content}</td>
			</tr>		
		</c:forEach>
	</table>


	<!-- 分页组件区 -->
	<center>	
		<div id="pp" style="background:#efefef;border:1px solid #ccc;width:600px"></div> 
	</center>
	<script type="text/javascript">
		$("#pp").pagination({ 
			total:${pageBean.allObjectNum}, 
			pageSize:${pageBean.perPageNum},
			showPageList:false,
			showRefresh:false,
			pageNumber:${pageBean.currPageNum}
		}); 
		$("#pp").pagination({
			onSelectPage:function(pageNumber){
	$("form").attr("action","${pageContext.request.contextPath}/ArticleServlet?currPageNum="+pageNumber);
			$("form").submit();	
			}
		});
	</script>
	<script type="text/javascript">
 			$(":button").click(function(){
 			$("form").submit();	
 		});
 	</script>
  </body>
</html>
```
步六：在WebRoot目录下创建list2.jsp
```
<%@ page language="java" pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    <title>根据关键字分页查询所有信息</title>
  </head>
  <body>
	
	<!-- 输入区 -->
	<form action="${pageContext.request.contextPath}/ArticleServlet" method="POST">
		<input id="currPageNOID" type="hidden" name="currPageNO" value="1">
		<table border="2" align="center">
			<tr>
				<th>输入关键字：</th>
				<th><input type="text" name="keywords" maxlength="4" value="${requestScope.keywords}"/></th>
				<th><input type="submit" value="站内搜索"/></th>
			</tr>
		</table>
	</form>
	
	
	<!-- 输出区 -->
	<table border="2" align="center" width="60%">
		<tr>
			<th>编号</th>
			<th>标题</th>
			<th>内容</th>
		</tr>
		<c:forEach var="article" items="${requestScope.pageBean.articleList}">
			<tr>
				<td>${article.id}</td>
				<td>${article.title}</td>
				<td>${article.content}</td>
			</tr>
		</c:forEach>
		<!-- 分页条 -->
		<tr>
			<td colspan="3" align="center">
				<a onclick="fy(1)" style="text-decoration:none;cursor:hand">
					【首页】
				</a>
				<c:choose>
					<c:when test="${requestScope.pageBean.currPageNO+1<=requestScope.pageBean.allPageNO}">
						<a onclick="fy(${requestScope.pageBean.currPageNO+1})" style="text-decoration:none;cursor:hand">
							【下一页】
						</a>
					</c:when>
					<c:otherwise>
							下一页
					</c:otherwise>
				</c:choose>
				<c:choose>
					<c:when test="${requestScope.pageBean.currPageNO-1>0}">
						<a onclick="fy(${requestScope.pageBean.currPageNO-1})" style="text-decoration:none;cursor:hand">
							【上一页】
						</a>
					</c:when>
					<c:otherwise>
							上一页
					</c:otherwise>
				</c:choose>
				<a onclick="fy(${requestScope.pageBean.allPageNO})" style="text-decoration:none;cursor:hand">
					【未页】
				</a>
			</td>
		</tr>
	</table>

	<script type="text/javascript">
		function fy(currPageNO){
			document.getElementById("currPageNOID").value = currPageNO;
			document.forms[0].submit();
		}
	</script>
	
  </body>
</html>
```

### activiti

Activiti5是由Alfresco软件在2010年5月17日发布的业务流程管理（BPM）框架，它是覆盖了业务流程管理、工作流、服务协作等领域的一个开源的、灵活的、易扩展的可执行流程语言框架。Activiti基于Apache许可的开源BPM平台，创始人Tom Baeyens是JBoss jBPM的项目架构师，它特色是提供了eclipse插件，开发人员可以通过插件直接绘画出业务
流程图。
工作流概念
   oa系统、电子政务   经常采用工作流

activiti工作流包括
    1. 工作流引擎 ：ProcessEngine ，它是Activiti的核心类，由该类可以获取其他的服务实例
                 （历史服务、仓库服务、任务服务、用户参与者服务）
    2. BPMN  ：UML ：用例图、类图、时序图 
       业务流程建模与标注（Business Process Model and Notation，BPMN) ，
       描述流程的基本符号，包括这些图元如何组合成一个业务流程图（Business Process Diagram）
    3. 数据库 
       Activiti的后台是有数据库的支持，所有的表都以ACT_开头。
       第二部分是表示表的用途的两个字母标识。 用途也和服务的API对应
       Activiti的工作流数据库有23张表    
         要保存流程定义 
        - act_ge_*  ：通用表
        - act_hi_*  ：历史流程相关表 
        - act_re_*  ：仓库表：保存流程定义
        - act_ru_*  ：保存流程运行相关的表  
        - act_id_*  ：用户参与相关的表  
    4. activiti.cfg.xml  
        Activiti核心配置文件，配置流程引擎创建工具的基本参数和数据库连接参数
    5. logging.properties    ：log4j
 
![a4](https://volc1612.gitee.io/blog/images/interview-question/a4.png)
模拟activiti执行流程框架
1.创建activiti project工程，不关联项目，添加所有的activiti框架的jar包
1.2.配置ProcessEngineConfiguration工作流引擎。activiti.cfg.xml配置文件和log4j.properties

2.设置流程定义图bpmn(业务流程建模标注)
```
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-2.5.xsd
http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.0.xsd">

 <!-- 配置 ProcessEngineConfiguration  -->
 <bean id="processEngineConfiguration" class="org.activiti.engine.impl.cfg.StandaloneProcessEngineConfiguration">
   <!-- 配置数据库连接 -->
 <property name="jdbcDriver" value="com.mysql.jdbc.Driver"></property>
 <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/activitiDB?createDatabaseIfNotExist=true&amp;useUnicode=true&amp;characterEncoding=utf8"></property>
 <property name="jdbcUsername" value="root"></property>
 <property name="jdbcPassword" value="root"></property>
  <!-- 配置创建表策略 :没有表时，自动创建 -->
  <property name="databaseSchemaUpdate" value="true"></property>
 </bean>
</beans>
<!-- 
	<c3p0-config>
	<default-config>
		<property name="driverClass">oracle.jdbc.driver.OracleDriver</property>
		<property name="jdbcUrl">jdbc:oracle:thin:@127.0.0.1:1521:orcl</property>
		<property name="user">scott</property>
		<property name="password">tiger</property>
		<property name="acquireIncrement">2</property>
		<property name="initialPoolSize">5</property>
		<property name="minPoolSize">1</property>
		<property name="maxPoolSize">5</property>
	</default-config>
</c3p0-config>
 -->
```
   右击diagram目录新建一个activiti diagram流程图，创建流程图，编写每一个流程的信息
   General和main config配置信息
 
![a5](https://volc1612.gitee.io/blog/images/interview-question/a5.png)
![a6](https://volc1612.gitee.io/blog/images/interview-question/a6.png)
指定当前流程有哪个人来审批，指定人名

![a7](https://volc1612.gitee.io/blog/images/interview-question/a7.png) 

流程引擎类结构：
![a8](https://volc1612.gitee.io/blog/images/interview-question/a8.png)   


3.通过代码的形式在ActivitiTest.java类中创建流程引擎对象。代替activiti.cfg.xml中的配置(选其一)

```java
import java.util.List;
import org.activiti.engine.ProcessEngine;
import org.activiti.engine.ProcessEngineConfiguration;
import org.activiti.engine.ProcessEngines;
import org.activiti.engine.RepositoryService;
import org.activiti.engine.RuntimeService;
import org.activiti.engine.TaskService;
import org.activiti.engine.repository.Deployment;
import org.activiti.engine.repository.DeploymentBuilder;
import org.activiti.engine.runtime.ProcessInstance;
import org.activiti.engine.task.Task;
import org.activiti.engine.task.TaskQuery;
import org.junit.Test;

/**
 * 模拟activiti工作流的框架
 * @author Administrator
 */
public class ActivitiTest {

	private ProcessEngine defaultProcessEngine = ProcessEngines.getDefaultProcessEngine();
	@Test
	//创建activiti涉及的数据库和表
	public void createProcessEnginer(){
		/**
		 * 1.通过代码形式创建
		 * 	1.1获取ProcessEngineConfiguration对象
		 * 	1.2设置数据路连接属性
		 *  1.3当没有表时设置创建表的策略
		 *  1.4通过ProcessEngineConfiguration创建ProcessEnginer对象
		 */ 
		/*
		//1.1获取ProcessEngineConfiguration对象
		ProcessEngineConfiguration configuration = ProcessEngineConfiguration.
				createStandaloneProcessEngineConfiguration();
		configuration.setJdbcDriver("com.mysql.jdbc.Driver");
		//1.2设置数据路连接属性
	//带参数url不存在则创建，设置字符集编码格式(自动创建activitidb数据库和23张表)	configuration.setJdbcUrl("jdbc:mysql://localhost:3306/activitiDB?createDatabaseIfNotExist=true"+
				"&useUnicode=true&characterEncoding=utf8");
		configuration.setJdbcUsername("root");
		configuration.setJdbcPassword("root");
		//1.3当没有表时设置创建表的策略
//		  public static final java.lang.String DB_SCHEMA_UPDATE_FALSE = "false";
//		  public static final java.lang.String DB_SCHEMA_UPDATE_CREATE_DROP = "create-drop";
//		  public static final java.lang.String DB_SCHEMA_UPDATE_TRUE = "true";//假如没有表会自动创建表
		configuration.setDatabaseSchemaUpdate("true");
		//1.4通过ProcessEngineConfiguration创建ProcessEnginer对象
		ProcessEngine buildProcessEngine = configuration.buildProcessEngine();
		System.out.println("流程引擎创建成功" + buildProcessEngine);
		*/
		
		/*
		 * 2.通过加载activit.cfg.xml获取流程引擎和自动创建数据库及表
               从类的加载路径中加载资源activiti.cfg.xml配置文件
		 */
		/*ProcessEngineConfiguration configuration = ProcessEngineConfiguration.
				createProcessEngineConfigurationFromResource("activiti.cfg.xml");//从类的加载路径
		ProcessEngine buildProcessEngine = configuration.buildProcessEngine();
		System.out.println("通过activiti.cfg.xml文件创建流程引擎对象" + buildProcessEngine);*/
		/*
		 * 3.通过默认加载类路径下activiti.cfg.xml
		 */
		System.out.println("通过加载默认的类路径下的配置文件方法获取流引擎对象" + defaultProcessEngine);
	}
}
```

4. 部署流程定义
```java
/*
	 * 部署流程定义 
	 */
	@Test
	public void deploy(){
		//获取流程引擎对象
		RepositoryService repositoryService = defaultProcessEngine.getRepositoryService();
		Deployment deploy = repositoryService.createDeployment()
				.addClasspathResource("diagrams/LeaveBill.bpmn")
				.addClasspathResource("diagrams/LeaveBill.png")
				.name("请假单流程图")//设置部署的名称
				.category("办公类别")//设置部署类别
				.deploy();//真正部署
		System.out.println("部署流程图的对象的id" + deploy.getId());
		System.out.println("部署流程图的对象的名称" + deploy.getName());
	}
```
5.执行流程
```java
//执行流程
	@Test
	public void startProcess(){
		String processValue = "leavebill";
		RuntimeService runtimeService = defaultProcessEngine.getRuntimeService();
		//取得流程实例
		ProcessInstance processInstanceByKey = runtimeService.startProcessInstanceByKey(processValue);
		System.out.println("流程实例的id" + processInstanceByKey.getId());
		System.out.println("流程定义的id" + processInstanceByKey.getProcessDefinitionId());
	}
```
6. 查询任务

```java
//查询任务
	@Test
	public void queryTask(){
		String name = "张三";
		TaskService taskService = defaultProcessEngine.getTaskService();
		//创建一个任务查询对象
		TaskQuery createTaskQuery = taskService.createTaskQuery();
		//查询办理人的任务列表
		List<Task> list = createTaskQuery.taskAssignee(name)
			.list();
		//查询到任务列表，遍历
		if(list != null && list.size() > 0){
			for(Task task : list){
				System.out.println("任务办理人" + task.getAssignee());
				System.out.println("任务id" + task.getId());
				System.out.println("任务名称" + task.getName());
			}
		}
	}
```
7.完成任务
```java
//完成任务
	@Test
	public void compileTask(){
		String taskId = "104";
		TaskService taskService = defaultProcessEngine.getTaskService();
		taskService.complete(taskId);
		System.out.println("执行任务完成");
	}
```

### Maven

Maven是一个采用纯Java编写的开源项目管理工具, Maven采用了一种被称之为Project Object Model (POM)概念来管理项目，所有的项目配置信息都被定义在一个叫做POM.xml的文件中, 通过该文件Maven可以管理项目的整个生命周期，包括清除、编译，测试，报告、打包、部署等等

Maven能够做什么
	Jar的声明式依赖性管理
	项目自动构建
使用需求
     - 企业岗位需求
     - 软件开发中遇到的问题
       1. jar包的依赖与管理  
         项目中有很多jar包： 
	   问题：不能确定jar包的完全正确性、不同技术框架版本的管理、jar包的依赖
       2. 自动构建项目
          - 软件开发： 可行性分析、需求分析、软件设计、软件开发、发布、运维
	  - 软件构建： 软件已经开发完毕，需要构建成一个产品进行发布
	    构建步骤：
	    清除--> 编译-->测试-->报告-->打包（jar\war）-->安装-->部署到远程
	  maven可以通过一个命令实现自动构建软件项目

pom.xml
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <!-- 当前pom的版本号 -->
  <modelVersion>4.0.0</modelVersion>
  <!-- groupId: 当前jar所属的命名空间 -->
  <groupId>cn.itcast.maven</groupId>
  <!-- 当前项目模块名称 -->
  <artifactId>Hello</artifactId>
  <!-- 当前项目的版本, SNAPSHOT镜像版 -->
  <version>0.0.1-SNAPSHOT</version>
	<!-- 当前模块需要依赖的相关jar包,也称为依赖管理, 所有被依赖的包都是通过"坐标"定位的 -->
	<dependencies>
	    <!-- 需要依赖junit 通过 groupId+artifactId+version来查找,如果本地没有则到中央仓库下载 -->
		<dependency>
			<!-- 当前jar所属的命名空间 -->
			<groupId>junit</groupId>
			<!-- 依赖的项目模块的名称 -->
			<artifactId>junit</artifactId>
			<!-- 依赖的版本号 -->
			<version>4.9</version>
			<!-- 依赖的范围, 有 test compile privlege -->
			<scope>test</scope>
		</dependency>		
	</dependencies>
</project>
```

使用Maven用例
1. 1.4.1.	HelloFriend目录结构
HelloFriend
 --src
 -----main
 ----------java
 ----------resources
 -----test
 ---------java
 ---------resources
 --pom.xml
建立pom.xml
```xml
<project 
xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>cn.itcast.maven</groupId>
	<artifactId>HelloFriend</artifactId>
	<version>1.0.0</version>
	<packaging>jar</packaging>
	<dependencies>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.9</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>cn.itcast.maven</groupId>
			<artifactId>Hello</artifactId>
			<version>1.0.0</version>
			<scope>compile</scope>
		</dependency>
	</dependencies>
</project>
```
在src/main/java/cn/itcast/maven目录下新建文件HelloFriend.java

```java
public class HelloFriend{
	public String sayHello(String name){
		return "Hello "+name+"!";
	}
}
```
在/src/test/java/cn/itcast/maven目录下新建测试文件HelloFriendTest.java

```java
import org.junit.Test;
import cn.itcast.maven.Hello;
public class HelloFriendTest {
	@Test
	public void tesHelloFriend(){
		HelloFriend helloFriend = new HelloFriend();
		String results = helloFriend.sayHelloToFriend("litingwei");
		System.out.println(results);		
	}
}
```
命令测试
```
在HelloFriend目录下执行 mvn clean mvn compile mvn test 都正常
在HelloFriend目录下执行命令mvn package 系统报错说没有找到依赖, 因为HelloFriend依赖Hello模块,但是此模块在个人仓库和中央仓库中并不存在
需要重新构建Hello第一个项目并安装到数据仓库, 在Hello根目录下执行mvn clean install, 就会部署的中央仓库中
```

生命周期
![a9](https://volc1612.gitee.io/blog/images/interview-question/a9.png) 
 


### Quartz(制定任务详细执行时间)
QuartzTask.java

```java
import java.text.SimpleDateFormat;
import java.util.Date;
public class QuartzTask {
	public void doSimpleTriggerTask() {
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		System.out.println("doing simpleTrigger task..." + sdf.format(new Date()));
	}
	public void doCronTriggerTask() {
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		System.out.println("doing cronTrigger task..." + sdf.format(new Date()));
	}
}
```

complain-spring.xml配置//在每月的月底下午的3点每分钟的第10秒执行，自动处理过期投诉数据
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation=" http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd 
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-3.0.xsd 
	http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd">
    
    <bean id="complainDao" class="cn.itcast.nsfw.complain.dao.impl.ComplainDaoImpl" parent="baseDao"></bean>
    
    <!-- 扫描service -->
    <context:component-scan base-package="cn.itcast.nsfw.complain.service.impl"></context:component-scan>
    
     <!-- 1、制定任务信息信息 -->
	<bean id="complainJobDetail" class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
		<!-- 设置执行对象 -->
		<property name="targetObject" ref="complainService"></property>
		<!-- 设置执行对象中对应的执行方法 -->
		<property name="targetMethod" value="autoDeal"></property>
		<!-- 是否可以同步执行；不可同步执行 -->
		<property name="concurrent" value="false"></property>
	</bean>
     <!-- 2、制定任务执行时机（任务执行触发器） -->
     <bean id="complainCronTrigger" class="org.springframework.scheduling.quartz.CronTriggerBean">
     	<!-- 设置任务详细信息 -->
     	<property name="jobDetail" ref="complainJobDetail"></property>
     	<!-- 设置任务执行时间点，cronExpression: 在每月的月底下午的3点每分钟的第10秒执行任务 -->
     	<property name="cronExpression" value="10 * 15 L * ?"></property>
     </bean>
     <!-- 3、设置调度工厂 -->
     <bean id="complainScheduler" class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
     	<property name="triggers">
     		<list>
     			<ref bean="complainCronTrigger"/>
     		</list>
     	</property>
     </bean>
</beans>
```

quartz-spring.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation=" http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd 
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-3.0.xsd 
	http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd">
    
   <!-- 注册一个普通bean -->
   <bean id="quartzTask" class="cn.itcast.nsfw.complain.QuartzTask"></bean>
   
   <!-- 1、制定任务信息信息 -->
   <bean id="jobDetail1" class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
   		   <!-- 设置执行对象 -->
   		   <property name="targetObject" ref="quartzTask"></property>
   		   <!-- 设置执行对象中对应的执行方法 -->
   		   <property name="targetMethod" value="doSimpleTriggerTask"></property>
   		   <!-- 是否可以同步执行；不可同步执行 -->
   		   <property name="concurrent" value="false"></property>
   </bean>
   <bean id="jobDetail2" class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
   		   <!-- 设置执行对象 -->
   		   <property name="targetObject" ref="quartzTask"></property>
   		   <!-- 设置执行对象中对应的执行方法 -->
   		   <property name="targetMethod" value="doCronTriggerTask"></property>
   		   <!-- 是否可以同步执行；不可同步执行 -->
   		   <property name="concurrent" value="false"></property>
   </bean>
   
   <!-- 2、制定任务执行时机（任务执行触发器） -->
   <bean id="simplerTrigger" class="org.springframework.scheduling.quartz.SimpleTriggerBean">
   		<!-- 设置任务详细 -->
   		<property name="jobDetail" ref="jobDetail1"></property>
   		<!-- 设置任务延迟执行时间 ；延迟2秒执行-->
   		<property name="startDelay" value="2000"></property>
   		<!-- 设置任务执行频率；执行频率为每4秒执行一下 -->
   		<property name="repeatInterval" value="2000"></property>
   </bean>
   <bean id="cronTrigger" class="org.springframework.scheduling.quartz.CronTriggerBean">
        <!-- 设置任务详细 -->
   		<property name="jobDetail" ref="jobDetail2"></property>
   		<!-- 设置任务执行时机，cron表达式 -->
   		<property name="cronExpression" value="* * * 18c * ?"></property>
   </bean>
   <!-- 3、设置调度工厂 -->
   <bean class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
   		<property name="triggers">
   			<list>
   				<!-- <ref bean="simplerTrigger"/> -->
   				<ref bean="cronTrigger"/>
   			</list>
   		</property>
   </bean>
</beans>
```


### 2345公司面试题

#### ++、--是否是线程安全的，为什么

i++和++i的线程安全分为两种情况：
1、如果i是局部变量（在方法里定义的），那么是线程安全的。因为局部变量是线程私有的，别的线程访问不到，其实也可以说没有线程安不安全之说，因为别的线程对他造不成影响。
2、如果i是全局变量（类的成员变量），那么是线程不安全的。因为如果是全局变量的话，同一进程中的不同线程都有可能访问到。
如果有大量线程同时执行i++操作，i变量的副本拷贝到每个线程的线程栈，当同时有两个线程栈以上的线程读取线程变量，假如此时是1的话，那么同时执行i++操作，再写入到全局变量，最后两个线程执行完，i会等于3而不会是2，所以，出现不安全性。

解决方法：
1、对 i++ 操作的方法加同步锁，同时只能有一个线程执行 i++ 操作
2、使用支持原子性操作的类，如 java.util.concurrent.atomic.AtomicInteger，它使用的是 CAS 算法，效率优于第 1 种

#### Spring IOC的理解和原理

参阅： https://www.cnblogs.com/xdp-gacl/p/4249939.html

##### 理解
Ioc—Inversion of Control，即“控制反转”，不是什么技术，而是一种设计思想。在Java开发中，Ioc意味着将你设计好的对象交给容器控制，而不是传统的在你的对象内部直接控制。
●谁控制谁，控制什么：传统Java SE程序设计，我们直接在对象内部通过new进行创建对象，是程序主动去创建依赖对象；而IoC是有专门一个容器来创建这些对象，即由Ioc容器来控制对 象的创建；谁控制谁？当然是IoC 容器控制了对象；控制什么？那就是主要控制了外部资源获取（不只是对象包括比如文件等）
●为何是反转，哪些方面反转了：有反转就有正转，传统应用程序是由我们自己在对象中主动控制去直接获取依赖对象，也就是正转；而反转则是由容器来帮忙创建及注入依赖对象；为何是反转？因为由容器帮我们查找及注入依赖对象，对象只是被动的接受依赖对象，所以是反转；哪些方面反转了？依赖对象的获取被反转了。

##### IOC(控制反转)原理
Spring容器高层视图
![b1](https://volc1612.gitee.io/blog/images/interview-question/b1.png)
Bean缓存池：HashMap实现

Spring 通过一个配置文件描述 Bean 及 Bean 之间的依赖关系，利用 Java 语言的反射功能实例化 Bean 并建立 Bean 之间的依赖关系
1.	BeanFactory 是 Spring 框架的基础设施，面向 Spring 本身，使用的是工厂设计模式
2.	ApplicationContext 面向使用 Spring 框架的开发者，几乎所有的应用场合我们都直接使用 ApplicationContext 而非底层的 BeanFactory。
###### BeanFactory
BeanFactory 是 Spring 框架的基础设施，面向 Spring 本身；
###### ApplicationContext
ApplicationContext 由 BeanFactory 派生而来，提供了更多面向实际应用的功能。
在BeanFactory 中，很多功能需要以编程的方式实现，而在 ApplicationContext 中则可以通过配置的方式实现。

##### DI(Dependency Injection，依赖注入)原理
IoC的一个重点是在系统运行中，动态的向某个对象提供它所需要的其他对象。这一点是通过DI（Dependency Injection，
依赖注入）来实现的。比如对象A需要操作数据库，以前我们总是要在A中自己编写代码来获得一个Connection对象，有了 
spring我们就只需要告诉spring，A中需要一个Connection，至于这个Connection怎么构造，何时构造，A不需要知道。
在系统运行时，spring会在适当的时候制造一个Connection



总结：
1.	SpringIOC容器主要有继承体系底层的BeanFactory、高层的ApplicationContext和WebApplicationContext
2.	Bean有自己的生命周期
3.	容器启动原理：Spring应用的IOC容器通过tomcat的Servlet或Listener监听启动加载；Spring MVC的容器由DispatchServlet作为入口加载；Spring容器是Spring MVC容器的父容器
4.	容器加载Bean原理：
BeanDefinitionReader读取Resource所指向的配置文件资源，然后解析配置文件。配置文件中每一个<bean>解析成一个BeanDefinition对象，并保存到BeanDefinitionRegistry中；
容器扫描BeanDefinitionRegistry中的BeanDefinition；调用InstantiationStrategy进行Bean实例化的工作；使用BeanWrapper完成Bean属性的设置工作；
单例Bean缓存池：Spring 在 DefaultSingletonBeanRegistry 类中提供了一个用于缓存单实例 Bean 的缓存器，它是一个用 HashMap 实现的缓存器，单实例的 Bean 以 beanName 为键保存在这个HashMap 中。
参考：https://zhuanlan.zhihu.com/p/29344811	



#### AOP面向切面原理
AOP是OOP的延续，是（Aspect Oriented Programming）的缩写，意思是面向切面编程。要理解AOP首先得弄明白代理的概念。对于代理看下点击打开链接这篇文章。
  AOP（Aspect Orient Programming），作为面向对象编程的一种补充，广泛应用于处理一些具有横切性质的系统级服务，如事务管理、安全检查、缓存、对象池管理、日志等。 AOP 实现的关键就在于 AOP 框架自动创建的 AOP 代理，AOP 代理则可分为静态代理和动态代理两大类，其中静态代理是指使用 AOP 框架提供的命令进行编译，从而在编译阶段就可生成 AOP 代理类，因此也称为编译时增强；而动态代理则在运行时借助于 JDK 动态代理、CGLIB 等在内存中"临时"生成 AOP 动态代理类，因此也被称为运行时增强。
知道这些其他的就是些配置了。
	
#### 堆和栈的区别
1.栈内存存储的是局部变量，基础数据，而堆内存存储的是实体(new的对象)；
2.栈内存的更新速度要快于堆内存，因为局部变量的生命周期很短；
3.栈内存存放的变量生命周期一旦结束就会被释放，而堆内存存放的实体会被垃圾回收机制不定时的回收。Java的堆是一个运行时数据区，堆是由垃圾回收来负责的，堆的优势是可以动态地分配内存大小。
4.如果栈内存没有可用的空间存储方法调用和局部变量，JVM会抛出java.lang.StackOverFlowError。
而如果是堆内存没有可用的空间存储生成的对象，JVM会抛出java.lang.OutOfMemoryError
5.栈中的数据共享的区别，和两个对象引用同时执行同一个堆中对象是不同的。通过一个对象的引用改变对象的数据，那么另一个对象的引用所指向的堆中的对象数据也会改变。而在栈中int a = 1;int b = 1; a改变不会影响b的值。


#### 对象的值相同(y.equals(x) == true),hash code相同吗，为什么
不一定相同。正常情况下，因为equals()方法比较的就是对象在内存中的值，如果值相同，那么Hashcode值也应该相同。但是如果不重写hashcode方法，就会出现不相等的情况。
通常有必要重写 hashCode 方法，以维护 hashCode 方法的常规协定，该协定声明相等对象必须具有相等的哈希码。

1.equals() 用于判断两个对象的数据、内容是否一致。 
2.hashCode()用于生成一个哈希码(相应对象整型的 hash 值)，便于HashMap使用，系统默认实现是计算对象的内存地址，转换成一个整数。 它们之间没什么联系，两个相等的对象生成的HASH码应该是相等的，但是HASH码相等并不能保证两个对象相等。关键在于你对相等的定义（也就是重写equals函数），与此同时，也应该重写hashCode函数保证上述关系。

== 是比较地址是否相等，JAVA中声明变量都是引用嘛，不同的引用，可能指向同一个地址。
equals 是比较值是否相等。

如果是基本变量，没有hashcode和equals方法，基本变量的比较方式就只有==；
如果是变量要比较实际内存中的内容，那就要用equals方法

#### 线程池的作用

第一：降低资源消耗。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。 
第二：提高响应速度。当任务到达时，任务可以不需要等到线程创建就能立即执行。 
第三：可以根据系统的承受能力，调整线程池中工作线线程的数目，防止因为因为消耗过多的内存，而把服务器累趴下(每个线程需要大约1MB内存，线程开的越多，消耗的内存也就越大，最后死机)。

#### Java实现线程同步的方式
1.同步方法
即有synchronized关键字修饰的方法。 由于java的每个对象都有一个内置锁，当用此关键字修饰方法时， 内置锁会保护整个方法。在调用该方法前，需要获得内置锁，否则就处于阻塞状态。
注： synchronized关键字也可以修饰静态方法，此时如果调用该静态方法，将会锁住整个类。
2. 同步代码块
即有synchronized关键字修饰的语句块。 被该关键字修饰的语句块会自动被加上内置锁，从而实现同步
    代码如： 
synchronized(object){ 
}

3. wait与notify
wait():使一个线程处于等待状态，并且释放所持有的对象的lock。
sleep():使一个正在运行的线程处于睡眠状态，是一个静态方法，调用此方法要捕捉InterruptedException异常。
notify():唤醒一个处于等待状态的线程，注意的是在调用此方法的时候，并不能确切的唤醒某一个等待状态的线程，而是由JVM确定唤醒哪个线程，而且不是按优先级。
Allnotity():唤醒所有处入等待状态的线程，注意并不是给所有唤醒线程一个对象的锁，而是让它们竞争。

4. 使用特殊域变量(volatile)实现线程同步

```java
//只给出要修改的代码，其余代码与上同
        class Bank {
            //需要同步的变量加上volatile
            private volatile int account = 100;
 
            public int getAccount() {
                return account;
            }
            //这里不再需要synchronized 
            public void save(int money) {
                account += money;
            }
        ｝
```

5. 使用重入锁实现线程同步

6. 使用阻塞队列实现线程同步

前面几种同步方式都是在底层实现的线程同步，但是我们在实际开发当中，应当尽量远离底层结构。 使用javaSE5.0版本中新增的java.util.concurrent包将有助于简化开发。 本小节主要是使用LinkedBlockingQueue<E>来实现线程的同步LinkedBlockingQueue<E>是一个基于已连接节点的，范围任意的blocking queue。队列是先进先出的顺序（FIFO），关于队列以后会详细讲解~
	LinkedBlockingQueue 类常用方法 LinkedBlockingQueue() : 创建一个容量为Integer.MAX_VALUE的LinkedBlockingQueue put(E e) : 
	在队尾添加一个元素，如果队列满则阻塞 size() : 返回队列中的元素个数 take() : 移除并返回队头元素，如果队列空则阻塞代码实例： 
	实现商家生产商品和买卖商品的同步

注：BlockingQueue<E>定义了阻塞队列的常用方法，尤其是三种添加元素的方法，我们要多加注意，当队列满时：
　　add()方法会抛出异常
　　offer()方法返回false
	put()方法会阻塞



#### 修饰符范围由大到小排序
（1）public：可以被所有其他类所访问。

（2）private：只能被自己访问和修改。

（3）protected：自身，子类及同一个包中类可以访问。

（4）default（默认）：同一包中的类可以访问，声明时没有加修饰符，认为是friendly。

| 修饰符 | 类内部 | 本包 | 子类	| 外部包 |
| public | √ | √ | √ | √ |
| protected | √ | √ | √ | × |
| default | √ |	√ | × | × |
| private |	√ |	× |	× | × |

#### ArrayList和LinkedList的在存储方式上区别
ArrayList和Vector都是使用数组方式存储数据
LinkedList使用双向链表实现存储

#### 查看linux服务器的内存使用情况的命令

top命令可以实时动态地查看系统的整体运行情况，是一个综合了多方信息监测系统性能和运行信息的实用工具。通过top命令所提供的互动式界面，用热键可以管理。
```
[root@VM_0_11_redhat ~]# top
top - 18:23:21 up 83 days, 17:49,  2 users,  load average: 0.00, 0.01, 0.05
Tasks:  75 total,   1 running,  74 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.7 us,  1.0 sy,  0.0 ni, 98.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1016516 total,    67664 free,   565684 used,   383168 buff/cache
KiB Swap:        0 total,        0 free,        0 used.   278832 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND                                                                                           
28924 root      20   0  740980  11396   1096 S  0.3  1.1 175:36.09 barad_agent                                                                                       
    1 root      20   0   43188   2352   1204 S  0.0  0.2   6:09.23 systemd  
```
查看RAM使用情况最简单的方法是通过/proc/meminfo
```
[root@VM_0_11_redhat ~]# cat /proc/meminfo
MemTotal:        1016516 kB
MemFree:           76552 kB
MemAvailable:     460296 kB
Buffers:           99964 kB
Cached:           368936 kB
SwapCached:            0 kB
Active:           585040 kB
Inactive:         232760 kB
Active(anon):     349044 kB
Inactive(anon):      144 kB
Active(file):     235996 kB
```

#### 添加优化查询速度索引的sql语句
需要添加普通类型的索引
普通索引（由关键字KEY或INDEX定义的索引）的唯一任务是加快对数据的访问速度。因此，应该只为那些最经常出现在查询条件（WHEREcolumn=）或排序条件（ORDERBYcolumn）中的数据列创建索引。只要有可能，就应该选择一个数据最整齐、最紧凑的数据列（如一个整数类型的数据列）来创建索引。
添加普通索引Sql：
INDEX(普通索引)
        mysql>ALTER  TABLE  `table_name`  ADD  INDEX index_name (  `column`  )

### mysql优化

#### sql优化

```sql
select * from a
left join b on b.id = a.id
left join c on c.id = b.id -- 慢的原因出在这
```
mysql查询存在直接关联和非直接关联的问题，这两种查询效率差别很大


#### List,set,map都同时继承了Conllection接口吗

不是
java.util.Collection [I]
+--java.util.List [I]
   +--java.util.ArrayList [C]
   +--java.util.LinkedList [C]
   +--java.util.Vector [C]
      +--java.util.Stack [C]
+--java.util.Set [I]
   +--java.util.HashSet [C]
   +--java.util.SortedSet [I]
      +--java.util.TreeSet [C]

java.util.Map [I]
+--java.util.SortedMap [I]
   +--java.util.TreeMap [C]
+--java.util.Hashtable [C]
+--java.util.HashMap [C]
+--java.util.LinkedHashMap [C]
+--java.util.WeakHashMap [C]
参考：http://android.blog.51cto.com/268543/400557

#### String a = new String(“abc”);创建了几个字符串对象

2个


#### 一个类的构造方法可以被其他对象调用吗

可以，但是不能像调用普通方法一样调用
在子类中的缺省构造器可以调用父类的缺省/非缺省构造器，在调用父类的缺省构造器时，
不用写super关键字直接可以调（因为super是隐式的）；在调用父类的非缺省构造器时，
必须用super（参数）显示调用。如下面的案例所示：
同一类中，在构造方法中可以调用本类的其他构造

```java
public class Zi extends Fu {
    public static String name = "123";


    public Zi() { // 子类无惨构造不能直接调用父类有参构造,需要将name参数提前加载，static先加载再加载构造
        super(name);
    }

    public static void main(String[] args) {
        new Zi();
    }
    /**
     * 1). 父类static（顺序按照代码书写顺序进行）
     2). 子类static（顺序按照代码书写顺序进行）
     3). 父类非static域、构造器（顺序按照代码书写顺序进行）
     4). 子类非static域、构造器（顺序按照代码书写顺序进行）
     */
}
```


### 闭包

闭包是javascript一个非常重要的特性，意味着当前作用域总能够访问外部作用域的变量，因为函数是javascript唯一拥有自身作用域的结构，因此闭包的创建依赖于函数。

#### 什么是闭包

```html
<html>
<body>
<h2>$rc.contextPath Hello World2!</h2>
</body>
<script>
    var foo = (function () {
        var name = "1123";
        return {
            get_name: function () {
                return name;
            },
            set_name: function (new_name) {
                name = new_name;
            }
        }
    })();
    alert(foo.get_name());
    /**
 * 1. 外部不能直接访问foo函数中的属性name，但是可以通过foo函数中的get_name函数访问到foo中的属性值name
 * 2. 并且通过foo中的get_name直接返回foo中属性name，让外部获取到foo中的属性值。
 * 3. 那么get_name这个函数就是将函数内部和函数外部连接起来的桥梁，这种特性称之为闭包
 *
 * 上述也是闭包的一个重要特性那就是读取函数内部变量
 */
</script>
</html>
```
含义:
近似正确的短答案：闭包就是一个函数把外部的那些不属于自己的对象也包含（闭合）进来了。
短答案：JavaScript中的闭包，无非就是变量解析的过程。

```html
<html>
<body>
<h2>$rc.contextPath Hello World2!</h2>
</body>
<script>
    (function () {
        var hello = "hello,world";

        function welcome(hi) {
            alert(hi);        //解析到作用域链的第一个对象的属性
            alert(hello);    //解析到作用域链的第二个对象的属性
        }

        welcome("It's easy");
    })();
</script>
</html>
```


运行结果很简单，一个弹窗It's easy.一个弹窗hello,world。
分析过程如下：
对于函数welcome()，定义welcome的时候会产生一个作用域链对象，为了表示方便，记作scopechain。scopechain是个有顺序的集合对象。
•	scopechain的第一个对象：为了方便表示记作sc1， sc1有若干属性，引用本函数的参数和局部变量，如sc1.hi ；
•	scopechain的第二个对象：为了方便表示记作sc2，sc2有若干属性，引用外层函数的参数和局部变量，如sc2.hello；
•	...
•	scopechain的最后一个对象：为了方便表示记作scn，scn引用的全局的执行环境对象，也就是window对象！，如scn.eval()；

这里之所以可以弹出hello,world，原因就是变量解析时在welcome函数作用域链的第一个对象上找不到hello属性，然后就去第二个对象上找去了（结果还真找到了）。
所以，JavaScript中的所谓的高大上的闭包其实很简单，根本上还是变量解析。而之所以可以实现，还是因为变量解析会在作用域链中依次寻找对应属性的导致的。

#### 闭包的作用
##### 读取函数内部变量
```javascript
var foo = (function () {

    var name = "1123";
    return {
        get_name: function () {
            return name;
        },
        set_name: function (new_name) {
            name = new_name;
        }
    }
})();
alert(foo.get_name());
```

##### 让变量始终保持在内存中

普通函数
```javascript
var foo = function () {
    var num = 2;
    return ++num;
}
alert(foo()); // 每次alert都是3
```

闭包函数
```javascript
var foo = (function () {
    var num = 2;
    return {
        addNum: function () {
            num += 1;
        },
        getNum: function () {
            return num;
        }
    }
})();
alert(foo.getNum()); // 2
foo.addNum();
alert(foo.getNum()); // 3
```

#### 使用闭包注意
（1）由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。
（2）闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。


### redis和memecache的不同
1、存储方式：
memecache 把数据全部存在内存之中，断电后会挂掉，数据不能超过内存大小
redis有部份存在硬盘上，这样能保证数据的持久性，支持数据的持久化（笔者注：有快照和AOF日志两种持久化方式，在实际应用的时候，要特别注意配置文件快照参数，要不就很有可能服务器频繁满载做dump）。
2、数据支持类型：
redis在数据支持上要比memecache多的多。
3、使用底层模型不同：
新版本的redis直接自己构建了VM 机制 ，因为一般的系统调用系统函数的话，会浪费一定的时间去移动和请求。
4、运行环境不同：
redis目前官方只支持LINUX 上去行，从而省去了对于其它系统的支持，这样的话可以更好的把精力用于本系统 环境上的优化，虽然后来微软有一个小组为其写了补丁。但是没有放到主干上
个人总结一下，有持久化需求或者对数据结构和处理有高级要求的应用，选择redis，其他简单的key/value存储，选择memcache

Redis支持数据类型：


### Mysql索引的种类和创建

MySQL主要提供2种方式的索引：B-Tree索引，Hash索引
B树索引具有范围查找和前缀查找的能力，对于有N节点的B树，检索一条记录的复杂度为O(LogN)。相当于二分查找。
哈希索引只能做等于查找，但是无论多大的Hash表，查找复杂度都是O(1)。
显然，如果值的差异性大，并且以等值查找（=、 <、>、in）为主，Hash索引是更高效的选择，它有O(1)的查找复杂度。
如果值的差异性相对较差，并且以范围查找为主，B树是更好的选择，它支持范围查找。

1.添加PRIMARY KEY（主键索引） 

```
mysql>ALTER TABLE `table_name` ADD PRIMARY KEY ( `column` ) 
```
2.添加UNIQUE(唯一索引) 

```
mysql>ALTER TABLE `table_name` ADD UNIQUE ( 
`column` 
) 
```
3.添加INDEX(普通索引) 

```
mysql>ALTER TABLE `table_name` ADD INDEX index_name ( `column` ) 
```
4.添加FULLTEXT(全文索引) 

```
mysql>ALTER TABLE `table_name` ADD FULLTEXT ( `column`) 
```
5.添加多列索引

```
mysql>ALTER TABLE `table_name` ADD INDEX index_name ( `column1`, `column2`, `column3` )
```
联合索引key index (a,b,c)，可以支持a | a,b| a,b,c 3种组合进行查找，但不支持 b,c进行查找。

### mysql索引使用场景

```
show status like 'Handler_read%';
```
```
Handler_read_first	75
Handler_read_key	3193
Handler_read_last	0
Handler_read_next	198
Handler_read_prev	0
Handler_read_rnd	304
Handler_read_rnd_next	5621
```
handler_read_key:这个值越高越好，越高表示使用索引查询到的次数
handler_read_rnd_next:这个值越高，说明查询低效

### mysql索引失效

- 如果条件中有or，即使其中有条件带索引也不会使用(这也是为什么尽量少用or的原因)
　注意：要想使用or，又想让索引生效，只能将where字句中or条件中的每个列都加上索引
- 对于多列索引，不是使用的第一部分，则不会使用索引，而'张三%'会用到索引
- 如果列类型是字符串，那一定要在条件中将数据使用引号引用起来,否则不使用索引
如：EXPLAIN select * from test where name = 123; 因为name字段为varchar类型的索引，需要name='123'才走索引
- 某一列为不可null时，查询条件为is null不走索引，条件is not null也不走索引。因为索引上根本没Null值，不能利用到索引，只能全表扫描
- B-tree索引 is null不会走,is not null会走,位图索引 is null,is not null 都会走
- 联合索引的字段都可为null时，where条件中is null,is not null都遵循最左原则
- in和not in不一定会走索引，当mysql判断全表扫描速度大于走索引速度时，则不再走索引，exists不走索引并且不会影响其他条件走索引
- < 小于 > 大于 <= >= 这个根据实际查询数据来判断，如果全盘扫描速度比索引速度要快则不走索引


### mysql中FULLTEXT索引

```
# 创建
ALTER TABLE `test` ADD FULLTEXT INDEX test_name (`name`,`en_name`) # 索引名称和索引字段

# name,en_name字段必须是FULLTEXT的联合索引，否则执行下面语句会报错
select * FROM test WHERE MATCH(name,en_name) AGAINST('r hate');
```
查询结果

```
id 	name		en_name
1	i hate you	i like you
```
   
### 查看sql是否用到索引

```
explain select * from company_info where cname like '小%'
```
![d1](https://volc1612.gitee.io/blog/images/interview-question/d1.png)

1）、id列数字越大越先执行，如果说数字一样大，那么就从上往下依次执行，id列为null的就表是这是一个结果集，不需要使用它来进行查询。
 
2）、select_type列常见的有：
A：simple：表示不需要union操作或者不包含子查询的简单select查询。有连接查询时，外层的查询为simple，且只有一个
B：primary：一个需要union操作或者含有子查询的select，位于最外层的单位查询的select_type即为primary。且只有一个
C：union：union连接的两个select查询，第一个查询是dervied派生表，除了第一个表外，第二个以后的表select_type都是union
D：dependent union：与union一样，出现在union 或union all语句中，但是这个查询要受到外部查询的影响
E：union result：包含union的结果集，在union和union all语句中,因为它不需要参与查询，所以id字段为null
F：subquery：除了from字句中包含的子查询外，其他地方出现的子查询都可能是subquery
G：dependent subquery：与dependent union类似，表示这个subquery的查询要受到外部表查询的影响
H：derived：from字句中出现的子查询，也叫做派生表，其他数据库中可能叫做内联视图或嵌套select
 
3）、table
显示的查询表名，如果查询使用了别名，那么这里显示的是别名，如果不涉及对数据表的操作，那么这显示为null，如果显示为尖括号括起来的<derived N>就表示这个是临时表，后边的N就是执行计划中的id，表示结果来自于这个查询产生。如果是尖括号括起来的<union M,N>，与<derived N>类似，也是一个临时表，表示这个结果来自于union查询的id为M,N的结果集。
 
4）、type
依次从好到差：system，const，eq_ref，ref，fulltext，ref_or_null，unique_subquery，index_subquery，range，index_merge，index，ALL，除了all之外，其他的type都可以使用到索引，除了index_merge之外，其他的type只可以用到一个索引
A：system：表中只有一行数据或者是空表，且只能用于myisam和memory表。如果是Innodb引擎表，type列在这个情况通常都是all或者index
B：const：使用唯一索引或者主键，返回记录一定是1行记录的等值where条件时，通常type是const。其他数据库也叫做唯一索引扫描
C：eq_ref：出现在要连接过个表的查询计划中，驱动表只返回一行数据，且这行数据是第二个表的主键或者唯一索引，且必须为not null，唯一索引和主键是多列时，只有所有的列都用作比较时才会出现eq_ref
D：ref：不像eq_ref那样要求连接顺序，也没有主键和唯一索引的要求，只要使用相等条件检索时就可能出现，常见与辅助索引的等值查找。或者多列主键、唯一索引中，使用第一个列之外的列作为等值查找也会出现，总之，返回数据不唯一的等值查找就可能出现。
E：fulltext：全文索引检索，要注意，全文索引的优先级很高，若全文索引和普通索引同时存在时，mysql不管代价，优先选择使用全文索引
F：ref_or_null：与ref方法类似，只是增加了null值的比较。实际用的不多。
G：unique_subquery：用于where中的in形式子查询，子查询返回不重复值唯一值
H：index_subquery：用于in形式子查询使用到了辅助索引或者in常数列表，子查询可能返回重复值，可以使用索引将子查询去重。
I：range：索引范围扫描，常见于使用>,<,is null,between ,in ,like等运算符的查询中。
J：index_merge：表示查询使用了两个以上的索引，最后取交集或者并集，常见and ，or的条件使用了不同的索引，官方排序这个在ref_or_null之后，但是实际上由于要读取所个索引，性能可能大部分时间都不如range
K：index：索引全表扫描，把索引从头到尾扫一遍，常见于使用索引列就可以处理不需要读取数据文件的查询、可以使用索引排序或者分组的查询。
L：all：这个就是全表扫描数据文件，然后再在server层进行过滤返回符合要求的记录。
 
5）、possible_keys
查询可能使用到的索引都会在这里列出来
 
6）、key
查询真正使用到的索引，select_type为index_merge时，这里可能出现两个以上的索引，其他的select_type这里只会出现一个。
 
7）、key_len
用于处理查询的索引长度，如果是单列索引，那就整个索引长度算进去，如果是多列索引，那么查询不一定都能使用到所有的列，具体使用到了多少个列的索引，这里就会计算进去，没有使用到的列，这里不会计算进去。留意下这个列的值，算一下你的多列索引总长度就知道有没有使用到所有的列了。要注意，mysql的ICP特性使用到的索引不会计入其中。另外，key_len只计算where条件用到的索引长度，而排序和分组就算用到了索引，也不会计算到key_len中。
 
8）、ref
如果是使用的常数等值查询，这里会显示const，如果是连接查询，被驱动表的执行计划这里会显示驱动表的关联字段，如果是条件使用了表达式或者函数，或者条件列发生了内部隐式转换，这里可能显示为func
 
9）、rows
这里是执行计划中估算的扫描行数，不是精确值
 
10）、extra
这个列可以显示的信息非常多，有几十种，常用的有
A：distinct：在select部分使用了distinc关键字
B：no tables used：不带from字句的查询或者From dual查询
C：使用not in()形式子查询或not exists运算符的连接查询，这种叫做反连接。即，一般连接查询是先查询内表，再查询外表，反连接就是先查询外表，再查询内表。
D：using filesort：排序时无法使用到索引时，就会出现这个。常见于order by和group by语句中
E：using index：查询时不需要回表查询，直接通过索引就可以获取查询的数据。
F：using join buffer（block nested loop），using join buffer（batched key accss）：5.6.x之后的版本优化关联查询的BNL，BKA特性。主要是减少内表的循环数量以及比较顺序地扫描查询。
G：using sort_union，using_union，using intersect，using sort_intersection：
using intersect：表示使用and的各个索引的条件时，该信息表示是从处理结果获取交集
using union：表示使用or连接各个使用索引的条件时，该信息表示从处理结果获取并集
using sort_union和using sort_intersection：与前面两个对应的类似，只是他们是出现在用and和or查询信息量大时，先查询主键，然后进行排序合并后，才能读取记录并返回。
H：using temporary：表示使用了临时表存储中间结果。临时表可以是内存临时表和磁盘临时表，执行计划中看不出来，需要查看status变量，used_tmp_table，used_tmp_disk_table才能看出来。
I：using where：表示存储引擎返回的记录并不是所有的都满足查询条件，需要在server层进行过滤。查询条件中分为限制条件和检查条件，5.6之前，存储引擎只能根据限制条件扫描数据并返回，然后server层根据检查条件进行过滤再返回真正符合查询的数据。5.6.x之后支持ICP特性，可以把检查条件也下推到存储引擎层，不符合检查条件和限制条件的数据，直接不读取，这样就大大减少了存储引擎扫描的记录数量。extra列显示using index condition
J：firstmatch(tb_name)：5.6.x开始引入的优化子查询的新特性之一，常见于where字句含有in()类型的子查询。如果内表的数据量比较大，就可能出现这个
K：loosescan(m..n)：5.6.x之后引入的优化子查询的新特性之一，在in()类型的子查询中，子查询返回的可能有重复记录时，就可能出现这个
 
除了这些之外，还有很多查询数据字典库，执行计划过程中就发现不可能存在结果的一些提示信息
 
11）、filtered
使用explain extended时会出现这个列，5.7之后的版本默认就有这个字段，不需要使用explain extended了。这个字段表示存储引擎返回的数据在server层过滤后，剩下多少满足查询的记录数量的比例，注意是百分比，不是具体记录数。


### Mysql各种存储引擎
数据库中的存储引擎其实是对使用了该引擎的表进行某种设置，数据库中的表设定了什么存储引擎，那么该表在数据存储方式、数据更新方式、数据查询性能以及是否支持索引等方面就会有不同的“效果”。在MySQL数据库中存在着多种引擎（不同版本的MySQL数据库支持的引擎不同），熟悉各种引擎才能在软件开发中应用引擎，从而开发出高性能的软件，MySQL数据库中的引擎有哪些呢？一般来说，MySQL有以下几种引擎：ISAM、MyISAM、HEAP（也称为MEMORY）、CSV、BLACKHOLE、ARCHIVE、PERFORMANCE_SCHEMA、InnoDB、 Berkeley、Merge、Federated和Cluster/NDB等，除此以外我们也可以参照MySQL++ API创建自己的数据库引擎。下面逐次介绍一下各种引擎：
        ISAM
        该引擎在读取数据方面速度很快，而且不占用大量的内存和存储资源；但是ISAM不支持事务处理、不支持外来键、不能够容错、也不支持索引。该引擎在包括MySQL 5.1及其以上版本的数据库中不再支持。
        MyISAM
        该引擎基于ISAM数据库引擎，除了提供ISAM里所没有的索引和字段管理等大量功能，MyISAM还使用一种表格锁定的机制来优化多个并发的读写操作，但是需要经常运行OPTIMIZE TABLE命令，来恢复被更新机制所浪费的空间，否则碎片也会随之增加，最终影响数据访问性能。MyISAM还有一些有用的扩展，例如用来修复数据库文件的MyISAMChk工具和用来恢复浪费空间的 MyISAMPack工具。MyISAM强调了快速读取操作，主要用于高负载的select，这可能也是MySQL深受Web开发的主要原因：在Web开发中进行的大量数据操作都是读取操作，所以大多数虚拟主机提供商和Internet平台提供商（Internet Presence Provider，IPP）只允许使用MyISAM格式。
MyISAM类型的表支持三种不同的存储结构：静态型、动态型、压缩型。
        静态型：指定义的表列的大小是固定（即不含有：xblob、xtext、varchar等长度可变的数据类型），这样MySQL就会自动使用静态MyISAM格式。使用静态格式的表的性能比较高，因为在维护和访问以预定格式存储数据时需要的开销很低；但这种高性能是以空间为代价换来的，因为在定义的时候是固定的，所以不管列中的值有多大，都会以最大值为准，占据了整个空间。
        动态型：如果列（即使只有一列）定义为动态的（xblob, xtext, varchar等数据类型），这时MyISAM就自动使用动态型，虽然动态型的表占用了比静态型表较少的空间，但带来了性能的降低，因为如果某个字段的内容发生改变则其位置很可能需要移动，这样就会导致碎片的产生，随着数据变化的增多，碎片也随之增加，数据访问性能会随之降低。
        对于因碎片增加而降低数据访问性这个问题，有两种解决办法：
        a、尽可能使用静态数据类型；
        b、经常使用optimize table table_name语句整理表的碎片，恢复由于表数据的更新和删除导致的空间丢失。如果存储引擎不支持 optimize table table_name则可以转储并        重新加载数据，这样也可以减少碎片；
        压缩型：如果在数据库中创建在整个生命周期内只读的表，则应该使用MyISAM的压缩型表来减少空间的占用。
        HEAP（也称为MEMORY）
        该存储引擎通过在内存中创建临时表来存储数据。每个基于该存储引擎的表实际对应一个磁盘文件，该文件的文件名和表名是相同的，类型为.frm。该磁盘文件只存储表的结构，而其数据存储在内存中，所以使用该种引擎的表拥有极高的插入、更新和查询效率。这种存储引擎默认使用哈希（HASH）索引，其速度比使用B-+Tree型要快，但也可以使用B树型索引。由于这种存储引擎所存储的数据保存在内存中，所以其保存的数据具有不稳定性，比如如果mysqld进程发生异常、重启或计算机关机等等都会造成这些数据的消失，所以这种存储引擎中的表的生命周期很短，一般只使用一次。
        CSV（Comma-Separated Values逗号分隔值）
        使用该引擎的MySQL数据库表会在MySQL安装目录data文件夹中的和该表所在数据库名相同的目录中生成一个.CSV文件（所以，它可以将CSV类型的文件当做表进行处理），这种文件是一种普通文本文件，每个数据行占用一个文本行。该种类型的存储引擎不支持索引，即使用该种类型的表没有主键列；另外也不允许表中的字段为null。
        BLACKHOLE（黑洞引擎）
        该存储引擎支持事务，而且支持mvcc的行级锁，写入这种引擎表中的任何数据都会消失，主要用于做日志记录或同步归档的中继存储，这个存储引擎除非有特别目的，否则不适合使用。详见博客《BlackHole 存储引擎》
        ARCHIVE
        该存储引擎非常适合存储大量独立的、作为历史记录的数据。区别于InnoDB和MyISAM这两种引擎，ARCHIVE提供了压缩功能，拥有高效的插入速度，但是这种引擎不支持索引，所以查询性能较差一些。
        PERFORMANCE_SCHEMA
        该引擎主要用于收集数据库服务器性能参数。这种引擎提供以下功能：提供进程等待的详细信息，包括锁、互斥变量、文件信息；保存历史的事件汇总信息，为提供MySQL服务器性能做出详细的判断；对于新增和删除监控事件点都非常容易，并可以随意改变mysql服务器的监控周期，例如（CYCLE、MICROSECOND）。
        InnoDB
        该存储引擎为MySQL表提供了ACID事务支持、系统崩溃修复能力和多版本并发控制（即MVCC Multi-Version Concurrency Control）的行级锁;该引擎支持自增长列（auto_increment）,自增长列的值不能为空，如果在使用的时候为空则自动从现有值开始增值，如果有但是比现在的还大，则直接保存这个值; 该引擎存储引擎支持外键（foreign key） ,外键所在的表称为子表而所依赖的表称为父表。该引擎在5.5后的MySQL数据库中为默认存储引擎。
        Berkeley（BDB）
        该存储引擎支持COMMIT和ROLLBACK等其他事务特性。该引擎在包括MySQL 5.1及其以上版本的数据库中不再支持。
        Merge
        该引擎将一定数量的MyISAM表联合而成一个整体。参见博客《MySQL Merge存储引擎》
        Federated
        该存储引擎可以不同的Mysql服务器联合起来，逻辑上组成一个完整的数据库。这种存储引擎非常适合数据库分布式应用。
        Cluster/NDB
        该存储引擎用于多台数据机器联合提供服务以提高整体性能和安全性。适合数据量大、安全和性能要求高的场景。

### String,StringBuffer,StringBuild的区别
String字符串常量
StringBuffer字符串变量(线程安全)
StringBuild字符串变量(非线程安全)

1.	首先说运行速度，或者说是执行速度，在这方面运行速度快慢为：StringBuilder > StringBuffer > String
2.	再来说线程安全
　　在线程安全上，StringBuilder是线程不安全的，而StringBuffer是线程安全的
如果一个StringBuffer对象在字符串缓冲区被多个线程使用时，StringBuffer中很多方法可以带有synchronized关键字，所以可以保证线程是安全的，但StringBuilder的方法则没有该关键字，所以不能保证线程安全，有可能会出现一些错误的操作。所以如果要进行的操作是多线程的，那么就要使用StringBuffer，但是在单线程的情况下，还是建议使用速度比较快的StringBuilder。

总结：
String：适用于少量的字符串操作的情况
　　StringBuilder：适用于单线程下在字符缓冲区进行大量操作的情况
　　StringBuffer：适用多线程下在字符缓冲区进行大量操作的情况



### 开发中建造者模式有哪些

建造者模式：
```java
public interface Sender {  
    public void Send();  
}  
public class Builder {  
      
    private List<Sender> list = new ArrayList<Sender>();  
      
    public void produceMailSender(int count){  
        for(int i=0; i<count; i++){  
            list.add(new MailSender());  
        }  
    }  
      
    public void produceSmsSender(int count){  
        for(int i=0; i<count; i++){  
            list.add(new SmsSender());  
        }  
	} 
}
```

测试类
```java
public class Test {  
    public static void main(String[] args) {  
        Builder builder = new Builder();  
        builder.produceMailSender(10);  
    }
}
```

StringBuild就是建造者模式

### Mybatis中#和$的区别

1.	＃{}将传入的数据都当成一个字符串，会对自动传入的数据加一个双引号。如：order by #{id}，如果传入的值是111,那么解析成sql时的值为order by “111”, 如果传入的值是id，则解析成的sql为order by “id”。
2.	${}将传入的数据直接显示生成在sql中。如：order by 
${id}，如果传入的值是111,那么解析成sql时的值为order by 111, 如果传入的值是id，则解析成的sql为order 
by id。
3.	#方式能够很大程度防止sql注入。
4.	$方式无法防止Sql注入。
5.	$方式一般用于传入数据库对象，例如传入表名.


### HashMap底层实现原理
HashMap实际上是一个“链表散列”的数据结构，即数组和链表的结合体。

 
![b2](https://volc1612.gitee.io/blog/images/interview-question/b2.png)
由上图可以看出

HashMap是基于hashing的原理，我们使用put(key, value)存储对象到HashMap中，使用get(key)从HashMap中获取对象。当我们给put()方法传递键和值时，我们先对键调用hashCode()方法，返回的hashCode用于找到bucket位置来储存Entry对象。

简单地说，HashMap 在底层将 key-value 当成一个整体进行处理，这个整体就是一个 Entry 对象。HashMap 底层采用一个 Entry[] 数组来保存所有的 key-value 对，当需要存储一个 Entry 对象时，会根据hash算法来决定其在数组中的存储位置，在根据equals方法决定其在该数组位置上的链表中的存储位置；当需要取出一个Entry时，
也会根据hash算法找到其在数组中的存储位置，再根据equals方法从该位置上的链表中取出该Entry。

### 什么是hash碰撞

```java
/**
 * 不同的值它的hashcode有可能相同
 */
String a = "Aa";
String b = "BB";
int aa = a.hashCode();
int bb = b.hashCode();
System.out.println(aa);
System.out.println(bb);
```

结果hashcode返回int值都是2112，这就是hashmap发生碰撞的原因。


![d4](https://volc1612.gitee.io/blog/images/interview-question/d4.png)

上图索引位置如果是entry链那么这个hashcode就发生了碰撞。比如索引值为1的位置上，是一个entry链，每个entry的hashcode都相同，但是他们的key不同，当put元素的时候还需要遍历这个索引下的所有entry，找到相同的值并覆盖，找不到就在结尾添加新元素。


### 解决不同手机录制视频时编码不同导致web端无法播放的问题

使用ffmpeg工具将上传的视频都以h264来重新编码


### List,set,map区别

java.util.Collection [I]
+--java.util.List [I]
   +--java.util.ArrayList [C]
   +--java.util.LinkedList [C]
   +--java.util.Vector [C]
      +--java.util.Stack [C]
+--java.util.Set [I]
   +--java.util.HashSet [C]
   +--java.util.SortedSet [I]
      +--java.util.TreeSet [C]

java.util.Map [I]
+--java.util.SortedMap [I]
   +--java.util.TreeMap [C]
+--java.util.Hashtable [C]
+--java.util.HashMap [C]
+--java.util.LinkedHashMap [C]
+--java.util.WeakHashMap [C]


### JVM调优

https://blog.csdn.net/ldx891113/article/details/51735171


### websocket,socket和http
1.http协议是用在应用层的协议，他是基于tcp协议的，http协议建立链接也必须要有三次握手才能发送信息。
　　http链接分为短链接，长链接，短链接是每次请求都要三次握手才能发送自己的信息。即每一个request对应一个response。长链接是在一定的期限内保持链接。保持TCP连接不断开。客户端与服务器通信，必须要有客户端发起然后服务器返回结果。客户端是主动的，服务器是被动的。

2.WebSocket同HTTP一样也是应用层的协议，但是它是一种双向通信协议，是建立在TCP之上的。

连接过程握手过程
1. 浏览器、服务器建立TCP连接，三次握手。这是通信的基础，传输控制层，若失败后续都不执行。
2. TCP连接成功后，浏览器通过HTTP协议向服务器传送WebSocket支持的版本号等信息。（开始前的HTTP握手）
3. 服务器收到客户端的握手请求后，同样采用HTTP协议回馈数据。
4. 当收到了连接成功的消息后，通过TCP通道进行传输通信。

3. Socket其实并不是一个协议，而是为了方便使用TCP或UDP而抽象出来的一层，是位于应用层和传输控制层之间的一组接口。


http和websocket区别
WebSocket在建立握手连接时，数据是通过http协议传输的，但在建立连接之后，真正的数据传输阶段是不需要http协议参与的。
websocket传输的数据是二进制流，是以帧为单位的，http传输的是明文传输，是字符串传输，WebSocket的数据帧有序。
websocket的请求的头部和http请求头部不同，见上文

### 处理创建订单号并发

```java
import java.util.UUID;

/**
 * 高并发生成订单号
 */
public class GenerateOrderId {
    public static String getOrderIdByUUId() {
        int machineId = 1;//最大支持1-9个集群机器部署
        int hashCodeV = UUID.randomUUID().toString().hashCode();
        if (hashCodeV < 0) {//有可能是负数
            hashCodeV = -hashCodeV;
        }
        // 0 代表前面补充0
        // 15 hashCodeV 不足15位时在前面补上0
        // d 代表参数为正数型
        System.out.println(hashCodeV);
        return machineId + String.format("%015d", hashCodeV);
    }
    public static void main(String[] args) {
        System.out.println(getOrderIdByUUId());
    }
	// hashCodeV = 2042282884
	// orderId = 1000002042282884
}

```

### session是什么时候创建的
请求一个servlet时不创建session，请求jsp时session对象会创建

Jsp本质就是servlet
Jsp源码如下：

![b3](https://volc1612.gitee.io/blog/images/interview-question/b3.png)
只有在servlet中调用request.getSession();或者request.getSession(true);服务器才会产生session。

### 存储过程优点
存储过程的优点： 
1.存储过程只在创造时进行编译，以后每次执行存储过程都不需再重新编译，而一般SQL语句每执行一次就编译一次,所以使用存储过程可提高数据库执行速度。 
2.当对数据库进行复杂操作时(如对多个表进行Update,Insert,Query,Delete时），可将此复杂操作用存储过程封装起来与数据库提供的事务处理结合一起使用。 
3.存储过程可以重复使用,可减少数据库开发人员的工作量 。
4.安全性高,可设定只有某此用户才具有对指定存储过程的使用权。
函数，存储过程，触发器的区别

### 函数和存储过程
函数只返回一个变量，存储过程可以返回多个值

存储过程和触发器
触发器主要是通过事件执行触发而被执行的，而存储过程可以通过存储过程名称名字而直接调用。
### 视图的作用
1.	简单性 可以过滤掉大量不需要的数据，产生一张虚拟表，方便对数据操作
2.	安全性 通过视图用户只能查看和修改所能看到的数据
3.	逻辑数据独立性 代码可以直接操作视图
4.	视图并不能提高查询速度

### Jquery动态添加一行方法
var trHTML = "<tr><td>...</td></tr>"
$("#tab").append(trHTML);//在table最后面添加一行
$("#tab tr:eq(2)").after(trHTML); // 在table的第3行后面添加一行
$("#tab tr:not(:first)").empty(); //清空table（除了第一行以外）

### Js赋值顺序
var a = 0
var c = 3;
b[a] =  a  = c

结果：
b[3] = 3;


### log4j日志级别

log4j定义了8个级别的log（除去OFF和ALL，可以说分为6个级别），优先级从高到低依次为：OFF、FATAL、ERROR、WARN、INFO、DEBUG、TRACE、 ALL。

ALL 最低等级的，用于打开所有日志记录。

TRACE designates finer-grained informational events than the DEBUG.Since:1.2.12，很低的日志级别，一般不会使用。

DEBUG 指出细粒度信息事件对调试应用程序是非常有帮助的，主要用于开发过程中打印一些运行信息。

INFO 消息在粗粒度级别上突出强调应用程序的运行过程。打印一些你感兴趣的或者重要的信息，这个可以用于生产环境中输出程序运行的一些重要信息，但是不能滥用，避免打印过多的日志。

WARN 表明会出现潜在错误的情形，有些信息不是错误信息，但是也要给程序员的一些提示。

ERROR 指出虽然发生错误事件，但仍然不影响系统的继续运行。打印错误和异常信息，如果不想输出太多的日志，可以使用这个级别。

FATAL 指出每个严重的错误事件将会导致应用程序的退出。这个级别比较高了。重大错误，这种级别你可以直接停止程序了。

OFF 最高等级的，用于关闭所有日志记录。

如果将log level设置在某一个级别上，那么比此级别优先级高的log都能打印出来。例如，如果设置优先级为WARN，那么OFF、FATAL、ERROR、WARN 4个级别的log能正常输出，而INFO、DEBUG、TRACE、 ALL级别的log则会被忽略。Log4j建议只使用四个级别，优先级从高到低分别是ERROR、WARN、INFO、DEBUG。

从我们实验的结果可以看出，log4j默认的优先级为ERROR或者WARN（实际上是ERROR）

### 五个运行时异常
AnnotationTypeMismatchException, 
ArithmeticException, 
ArrayStoreException, 
BufferOverflowException, 
BufferUnderflowException, 
CannotRedoException, 
CannotUndoException, 
**ClassCastException**, 
CMMException, 
ConcurrentModificationException, 
DOMException, 
EmptyStackException, 
EnumConstantNotPresentException, 
**EventException**, 
IllegalArgumentException, 
IllegalMonitorStateException, 
IllegalPathStateException, 
IllegalStateException, 
ImagingOpException, 
IncompleteAnnotationException, 
**IndexOutOfBoundsException**, 
**JMRuntimeException**, 
LSException, 
MalformedParameterizedTypeException, 
MirroredTypeException, 
MirroredTypesException, 
**MissingResourceException**, 
NegativeArraySizeException, 
**NoSuchElementException**, 
NoSuchMechanismException, 
**NullPointerException**, 
ProfileDataException, 
ProviderException, 
RasterFormatException, 
RejectedExecutionException, 
SecurityException, 
**SystemException**, 
TypeConstraintException, 
TypeNotPresentException, 
UndeclaredThrowableException, 
UnknownAnnotationValueException, 
UnknownElementException, 
**UnknownTypeException**, 
UnmodifiableSetException, 
UnsupportedOperationException, 
**WebServiceException**
### 字符流和字节流父类
Object

### Object常用方法
| 方法 | 返回值 |
| getClass() | Class<T> |
| hashCode() | int |
| toString() | String |
| clone() | Object |
| equals(Object) | boolean |
| wait() | void |
| notify() | void |
| notifyAll() | void |

### mysql锁表机制

### 如何解决mysql读写冲突

### Transactional

### spring和springmvc常用注解

#### spring常用注解

- @Component
	是所有受Spring 管理组件的通用形式，@Component注解可以放在类的头上，@Component不推荐使用。。 
- @Controller
	标注一个控制器组件类。 
- @Service
	标注一个业务逻辑组件类。 
- @Repository
	标注一个DAO组件类。
- @Bean
	@Bean是一个方法级别上的注解，主要用在@Configuration注解的类里，也可以用在@Component注解的类里。添加的bean的id为方法名
定义bean,下面是@Configuration里的一个例子

```java
@Configuration
public class AppConfig {

    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl();
    }

}
```
这个配置就等同于之前在xml里的配置
```
<beans>
    <bean id="transferService" class="com.acme.TransferServiceImpl"/>
</beans>
```
#### springmvc常用注解

- @Autowired：按类型注入
- @Resource(默认按名称注入)
	@Resource(name="dataSource")
	@Resource(type=DataSource.class)
- @RequestMapping
- @RequestParam(获取请求参数的值)

```java
@RestController
public class HelloController {
    @RequestMapping(value="/hello",method= RequestMethod.GET)
    public String sayHello(@RequestParam("id") Integer id){
        return "id:"+id;
    }
}
```
- @PathVaribale(获取url中的数据)

```java
@RestController
public class HelloController {
    @RequestMapping(value="/hello/{id}",method= RequestMethod.GET)
    public String sayHello(@PathVariable("id") Integer id){
        return "id:"+id;
    }
}
```
- @ResponseBody(返回类型为json)

### spring中bean是单利还是多例

单例的

### Spring中bean的生命周期

在spring中，从BeanFactory或ApplicationContext取得的实例为Singleton，也就是预设为每一个Bean的别名只能维持一个实例，而不是每次都产生一个新的对象使用Singleton模式产生单一实例，对单线程的程序说并不会有什么问题，但对于多线程的程序，就必须注意安全(Thread-safe)的议题，防止多个线程同时存取共享资源所引发的数据不同步问题。
然而在spring中 可以设定每次从BeanFactory或ApplicationContext指定别名并取得Bean时都产生一个新的实例：例如：
在spring中，singleton属性默认是true，只有设定为false，则每次指定别名取得的Bean时都会产生一个新的实例
一个Bean从创建到销毁，如果是用BeanFactory来生成,管理Bean的话，会经历几个执行阶段：




### 中断线程

```java
public void Thread.interrupt();
```

### wait/notify/notifyAll

- wait()、notify/notifyAll() 方法是Object的本地final方法，无法被重写
- wait()使当前线程阻塞，前提是 必须先获得锁，一般配合synchronized 关键字使用，即，一般在synchronized 同步代码块里使用 wait()、notify/notifyAll() 方法
- notify/notifyAll() 的执行只是唤醒沉睡的线程，而不会立即释放锁，锁的释放要看代码块的具体执行情况(不是按线程优先级来唤醒)
- notify方法只唤醒一个等待（对象的）线程并使该线程开始执行。所以如果有多个线程等待一个对象，这个方法只会唤醒其中一个线程，选择哪个线程取决于操作系统对多线程管理的实现。notifyAll 会唤醒所有等待(对象的)线程，尽管哪一个线程将会第一个处理取决于操作系统的实现。如果当前情况下有多个线程需要被唤醒，推荐使用notifyAll 方法。比如在生产者-消费者里面的使用，每次都需要唤醒所有的消费者或是生产者，以判断程序是否可以继续往下执行
- wait() 需要被try catch包围，中断也可以使wait等待的线程唤醒

### while与do-while

while判断条件是否满足如果不满足执行while后面的代码，如果满足则执行while里面的代码，直到不满足while条件时才执行while后面的代码
do-while先执行do里面的代码，再判断while是否满足，如果满足继续执行do里面的代码，直到不满足while条件时，do里面的代码将再执行，而继续执行do-while后面的代码

### synchronized

#### 生产-消费多线程安全案例

```java

import java.util.ArrayList;
import java.util.List;

/**
 * 多线程安全性，生产者与消费者案例
 * synchronized同步关键字的使用
 * 放在方法上时，锁为synchronized修饰方法所在的那个对象
 */
public class Plate {

    // 容器
    List<Object> eggs = new ArrayList<Object>();

    // 从容器中获取资源动作
    public synchronized Object getEgg() {
        while (eggs.size() == 0) {
            try {
                // 释放锁，本线程等待其他线程唤醒
                wait();
            } catch (InterruptedException e) {
            }
        }

        Object egg = eggs.get(0);
        // 清空盘子
        eggs.clear();
        // 只能唤醒阻塞中的一个线程
        notify();
        System.out.println("拿到鸡蛋");
        return egg;
    }

    // 向容器中添加资源动作
    public synchronized void putEgg(Object egg) throws InterruptedException {
        while (eggs.size() > 0) {
            try {
                // System.out.println("执行了putEgg中的wait方法");
                // 生产者锁释放，让消费者消费
                wait();
            } catch (InterruptedException e) {
            }
        }
        // 往盘子里放鸡蛋
        eggs.add(egg);
        // System.out.println("执行了putEgg中的notify方法");
        notify();
        Thread.sleep(1000);
        // System.out.println("putEgg中的notfy方法执行结束");
        System.out.println("放入鸡蛋");
    }

    // 使用多线程生产
    static class AddThread extends Thread {
        private Plate plate;
        private Object egg = new Object();

        public AddThread(Plate plate) {
            this.plate = plate;
        }

        public void run() {
            for (int i = 0; i < 5; i++) {
                try {
                    plate.putEgg(egg);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    // 使用多线程消费
    static class GetThread extends Thread {
        private Plate plate;

        public GetThread(Plate plate) {
            this.plate = plate;
        }

        public void run() {
            for (int i = 0; i < 5; i++) {
                plate.getEgg();
            }
        }
    }

    public static void main(String args[]) {
        try {
            // 同一个资源对象Plate，资源对象中synchronized的方法之间才生效(互斥锁)
            Plate plate = new Plate();
            Thread add = new Thread(new AddThread(plate));
            Thread get = new Thread(new GetThread(plate));
            /**
             * 线程执行过程：
             * 	有两个[新建状态]的线程add线程和get线程同时共享Plate类对象。
             * 	add.start(); // add线程首先进入[就绪状态]，等待执行
             get.start(); // get线程进入就绪，等待执行
             * 	add线程执行putEgg方法[运行状态]：
             * 	add线程启动，执行putEgg方法时获取Plate类对象锁，此时get线程不能获取Plate类对象锁,因此执行不了getEgg方法。
             * 	当add线程执行putEgg方法中的notify()方法后，add线程的锁并没有释放掉，而是唤醒锁(Plate类对象)的阻塞的线程(程序开始并没有阻塞的线程)到就绪，putEgg继续执行，打印出放入鸡蛋
             * 	当add线程执行完putEgg方法方法后，释放掉Plate类锁对象，此时盘子中有一个鸡蛋。这时get线程有可能获取锁对象开始执行(要不要执行取决于cpu会不会分配资源给get线程)。
             *	当add线程释放掉锁后，不一定执行get线程
             *		1.如果执行get线程，get线程获取锁执行getEgg方法，此时add线程就无法获取Plate对象锁，无法获取CUP的使用权，从而无法执行putEgg方法，add线程的这种状态称为[阻塞状态]。此时盘子中有鸡蛋，会执行clear方法，盘子中鸡蛋被清空，继续执行notify方法，将会唤醒阻塞的add线程处于[运行状态]，但此时add线程并没有获取锁，所以get线程继续执行，打印出拿到鸡蛋。释放掉锁，get线程执行结束。
             *		2.如果仍然执行add线程，add线程获取锁执行putEgg方法，此时盘子中有鸡蛋，会执行wait方法，进入阻塞等待其他线程唤醒add线程，这时add线程释放掉锁，此时盘子中有鸡蛋。此时的get线程正处于[运行状态]，而add线程又释放掉了锁，这时get线程会立即拿到锁，这时get线程获取锁执行getEgg方法，清空盘子中的鸡蛋后，继续执行notify方法唤醒其他线程，add线程进入[运行状态]，等待执行，get线程继续执行getEgg方法，打印出拿到鸡蛋，并释放掉锁，此时盘子清空了。
             *  当线程执行完了或异常退出了run方法，该线程状态处于[死亡状态]线程声明周期结束
             *
             */
            add.start();
            get.start();
            // join方法让主线程add和主线程get执行完后才能执行后面的代码
            add.join();
            get.join();
        } catch (Exception e) {
            e.printStackTrace();
        }
        System.out.println("测试结束");
    }
}
```
#### 类锁与对象锁

synchronized涉及到类锁和对象锁，在Task2 中定义四个方法 doLongTimeTaskA和doLongTimeTaskB是类锁，而doLongTimeTaskC和doLongTimeTaskD是对象锁。

任务对象

```java
public class Task2 {

    public synchronized static void doLongTimeTaskA() {
        System.out.println("name = " + Thread.currentThread().getName() + ", begain");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("name = " + Thread.currentThread().getName() + ", end");
    }

    public synchronized static void doLongTimeTaskB() {
        System.out.println("name = " + Thread.currentThread().getName() + ", begain");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("name = " + Thread.currentThread().getName() + ", end");
    }

    public synchronized void doLongTimeTaskC() {
        System.out.println("name = " + Thread.currentThread().getName() + ", begain");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("name = " + Thread.currentThread().getName() + ", end");
    }

    public void doLongTimeTaskD(LockObject lock) {
        synchronized (lock){
            System.out.println("name = " + Thread.currentThread().getName() + ", begain");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("name = " + Thread.currentThread().getName() + ", end");
        }

    }

}
```
ThreadA
```java

public class ThreadA extends Thread {

    private Task2 mTask2;

    public ThreadA(Task2 tk) {
        mTask2 = tk;
    }

    public void run() {
        mTask2.doLongTimeTaskC();
    }
}
```
ThreadB

```java

public class ThreadB extends Thread {

    private Task2 mTask2;

    public ThreadB(Task2 tk) {
        mTask2 = tk;
    }

    public void run() {
        mTask2.doLongTimeTaskC();
    }
}
```
测试类测试对象锁

```java

public class TestThread {
    public static void main(String[] args) {
        Task2 mTaska = new Task2();
        Task2 mTaskb = new Task2();
        ThreadA ta = new ThreadA(mTaska);
        ThreadB tb = new ThreadB(mTaskb);
        ta.setName("A");
        tb.setName("B");
        ta.start();
        tb.start();
    }

}
```

结果：

```
name = A, begain
name = B, begain
name = A, end
name = B, end
```
测试类锁，我们使ThreadA和ThreadB线程执行中使用Task2的类锁，修改如下

```java
public class ThreadA extends Thread {
    private Task2 mTask2;
    public ThreadA(Task2 tk) {
        mTask2 = tk;
    }
    public void run() {
        //mTask2.doLongTimeTaskC();
        mTask2.doLongTimeTaskA();
    }
}
```
```java
public class ThreadB extends Thread {
    private Task2 mTask2;
    public ThreadB(Task2 tk) {
        mTask2 = tk;
    }
    public void run() {
        // mTask2.doLongTimeTaskC();
        mTask2.doLongTimeTaskA();
    }
}
```
再次执行TestThread结果如下
```
name = A, begain
name = A, end
name = B, begain
name = B, end
```
在线程A执行完doLongTimeTaskA方法后，线程B才会获得该类锁接着去执行doLongTimeTaskA。也就是说，类锁对所有的该类对象都能起作用


**总结**：
- synchronized修饰的静态方法时，这个方法使用的类锁，synchronized修饰的非静态方法或代码块时，使用的是对象锁
- 如果多线程同时访问同一类的 类锁（synchronized 修饰的静态方法）以及对象锁（synchronized 修饰的非静态方法）这两个方法执行是异步的，原因：类锁和对象锁是2中不同的锁。 
- 类锁对该类的所有对象都能起作用，而对象锁不能

#### synchronized同步代码块的使用

LockObject对象锁

```java
public class LockObject {
}
```
ThreadA
```java
public class ThreadA extends Thread {
    private Task2 mTask2;
    private LockObject lock;
    public ThreadA(Task2 tk,LockObject lock) {
        this.mTask2 = tk;
        this.lock = lock;
    }
    public void run() {
        mTask2.doLongTimeTaskD(lock);
    }
}
```
ThreadB
```java
public class ThreadB extends Thread {
    private Task2 mTask2;
    private LockObject lock;

    public ThreadB(Task2 tk,LockObject lock) {
        this.mTask2 = tk;
        this.lock = lock;
    }

    public void run() {
        mTask2.doLongTimeTaskD(lock);
    }
}
```

测试类
```
public class TestThread {
    public static void main(String[] args) {
        Task2 mTaska = new Task2();
        Task2 mTaskb = new Task2();
        LockObject lockObject = new LockObject();
        ThreadA ta = new ThreadA(mTaska, lockObject);
        ThreadB tb = new ThreadB(mTaskb, lockObject);
        ta.setName("A");
        tb.setName("B");
        ta.start();
        tb.start();
    }
}
```

结果：
```
name = A, begain
name = A, end
name = B, begain
name = B, end
```

从结果来看synchronized代码块用的是对象锁，可以指定任意锁对象


### public类与非public类

加public表示全局类，该类可以import到任何类内。不加public默认为保留类，只能被同一个包内的其他类引用

### java8对list排序和分组

```java
public class Student {

    private String name;
    private String gender;
    private int age;

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Student(int age, String name) {
        super();
        this.age = age;
        this.name = name;
    }

    public Student(String name, String gender, int age) {
        super();
        this.age = age;
        this.name = name;
        this.gender = gender;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", gender='" + gender + '\'' +
                ", age=" + age +
                '}';
    }
}
```

排序
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

/**
 * list结合中根据某字段排序
 */
public class TestList {
    public static void main(String[] args) {

        List<Student> list = new ArrayList<Student>();
        list.add(new Student(5, "aa"));
        list.add(new Student(7, "bb"));
        list.add(new Student(6, "cc"));

        Collections.sort(list, new Comparator<Student>() {
            @Override
            public int compare(Student o1, Student o2) {
                //会把集合里面的对象两两传进方法里面比较，这里比较age，降序就O2-O1，升序就O1-O2
                return o2.getAge() - o1.getAge();
            }
        });
        //打印list每一项的age
        list.forEach(a -> System.out.println(a.getAge()));
    }
}

```

```
Student{name='bb', gender='null', age=7}
Student{name='cc', gender='null', age=6}
Student{name='aa', gender='null', age=5}
```

分组

```java
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.util.Arrays;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

public class Group {
    public static void main(String[] args) throws JsonProcessingException {

        ObjectMapper mapper = new ObjectMapper();
        List<Student> lists = Arrays.asList(new Student("zhangsan", "gril", 124),
                new Student("zhangsan", "gril", 123), new Student("wangwu", "man", 123));
        System.out.println("json对象:" + mapper.writeValueAsString(lists));
        // 根据list集合中元素的某个属性分组
        Map<String, List<Student>> result = lists.stream().collect(Collectors.groupingBy(Student::getName));
        System.out.println("单个属性值分组:" + result);
        // 根据list集合中元素的某个属性分组计数
        Map<String, Long> result1 = lists.stream().collect(Collectors.groupingBy(Student::getName, Collectors.counting()));
        System.out.println("单个属性值分组并计数:" + result1);
        // 根据分组的key值对结果进行排序、放进另一个map中并输出
        Map<String, Long> xMap = new HashMap<>();
        result1.entrySet().stream().sorted(Map.Entry.<String, Long>comparingByKey().reversed()) //reversed不生效
                .forEachOrdered(x -> xMap.put(x.getKey(), x.getValue()));
        System.out.println("单个属性值分组后对key值进行排序:" + xMap);
        // 分组，并统计其中一个属性值得sum或者avg:id总和
        Map<String, Integer> result3 = lists.stream().collect(
                Collectors.groupingBy(Student::getName, Collectors.summingInt(Student::getAge))
        );
        System.out.println("单个属性值分组后并计算某个属性总和:" + result3);
        // 对list集合中元素的多个属性分组
        Map<String, Map<String, List<Student>>> result4 = lists.stream().collect(Collectors.groupingBy(Student::getName, Collectors.groupingBy(Student::getGender)));
        System.out.println("多个属性值分组:" + result4);
    }
}
```
结果：
```
json对象:[{"name":"zhangsan","gender":"gril","age":124},{"name":"zhangsan","gender":"gril","age":123},{"name":"wangwu","gender":"man","age":123}]
单个属性值分组:{zhangsan=[Student{name='zhangsan', gender='gril', age=124}, Student{name='zhangsan', gender='gril', age=123}], wangwu=[Student{name='wangwu', gender='man', age=123}]}
单个属性值分组并计数:{zhangsan=2, wangwu=1}
单个属性值分组后对key值进行排序:{zhangsan=2, wangwu=1}
单个属性值分组后并计算某个属性总和:{zhangsan=247, wangwu=123}
多个属性值分组:{zhangsan={gril=[Student{name='zhangsan', gender='gril', age=124}, Student{name='zhangsan', gender='gril', age=123}]}, wangwu={man=[Student{name='wangwu', gender='man', age=123}]}}
```

### 静态代码块/构造方法/静态方法执行顺序

```java
public class TestMethod {

    public TestMethod() {
        System.out.println("启动类构造");
    }

    static {
        System.out.println("启动类静态代码块");

    }

    public static void main(String[] args) {
        System.out.println("启动类主程序开始");
        Ha ha = new Ha();
        System.out.println("启动类主程序结束");
    }
}

class Ha extends Fu {
    public static void xi() {
        System.out.println("子类静态方法");
    }

    public Ha() {
        System.out.println("子类构造");
    }

    static {
        System.out.println("子类静态代码块");
    }

}

class Fu {
    public Fu() {
        System.out.println("父类构造");
    }

    static {
        System.out.println("父类静态代码块");
    }

    public static void f() {
        System.out.println("父类静态方法");
    }
}
```
```
启动类静态代码块
启动类主程序开始
父类静态代码块
子类静态代码块
父类构造
子类构造
启动类主程序结束
```

### 数据类型知识点

```java
public class TestString {
    public static void main(String[] args) {
        Change change = new Change();
        // 字符串类型 值传递
        String a = "a";
        // 基本数据类型 值传递
        int b = 2;
        // 包装类型Integer 值传递
        Integer c = 2;
        long d = 3;
        /**
         * 在java中小数点默认是double(双精度)型的
         * float是单精度，往高精度转换成double需要强制转换,如double f1 = 1.1f;
         * double型需要强制转化成单精度float型，需在后面加上f或在前面加上(float)
         */
        float f2 = 1.2f;
        double f1 = 1;
        Float e = 3.19f;
        /**
         * Double e1 = 1; 编译错误，原因是
         * 包装类需要指定数据类型，类型不能自动转换，而基本数据类型可以自动转换(可以由高精度自动转换成低精度)
         */
        Double e1 = 3d;
        change.changeString(a);
        System.out.println(a);
        change.changeInt(b);
        System.out.println(b);
        change.changeInteger(c);
        System.out.println(c);
        // 对象
        User user = new User("张三", 10);
        change.changeUser(user);
        System.out.println(user);
        Integer i = 1;
        i += 1;
        /**
         * （1）其中Integer i =1；做了自动装箱（ 使用valueOf（）方法，int ---> Integer ）
         * （2）其中i +=1；先将Integer类型的 i 自动拆箱成 int（使用intValue（）方法，Integer--->int ），完成加法运行之后的 i 再装箱成Integer类型。
         */
        System.out.println(i);
    }
}

class Change {
    public String changeString(String str) {
        return str + "change";
    }

    public int changeInt(int i) {
        return i + 1;
    }

    public Integer changeInteger(Integer i) {
        return i + 1; // 执行完i+1后返回，而i++执行完返回代码再加1
    }

    public User changeUser(User user) {
        user.setName("hh");
        return user;

    }
}

class User {
    private String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

结果
```
a
2
2
User{name='hh', age=10}
2
```

### try/catch/finally

```java
public class TestTry {
    private int b = 1;

    public static void main(String[] args) {
        System.out.println(new TestTry().t());
    }

    public String t() {
        try {
            System.out.println("执行try代码块");
            int a = 1;
            // a = a / 0;
            System.out.println("报错后代码"); // 报错后代码不会执行
            return ++b + "1";
        } catch (Exception e) {
            e.printStackTrace();
            System.out.println("执行catch代码块");
            System.out.println("b = " + b);
            return "b";
        } finally {
            System.out.println("执行finally代码块");
            System.out.println("b = " + b);
            return "c";
        }
    }
}

```

```
执行try代码块
报错后代码
执行finally代码块
b = 2
c
```
先执行try或catch里面的return代码，最后执行finally中return中代码并返回。

### 静态变量和非静态变量的区别

1.JAVA中初始化的顺序:
        加载类；
        静态变量初始化；
        静态块；【其只能调度静态的，不能调度非静态的】
        成员变量；
        构造方法；

类的静态变量在内存中只有一个，java虚拟机在加载类的过程中为静态变量分配内存，静态变量位于方法区，被类的所有实例共享。静态变量可以直接通过类名进行访问，其生命周期取决于类的生命周期。
而实例变量取决于类的实例。每创建一个实例，java虚拟机就会为实例变量分配一次内存，实例变量位于堆区中，其生命周期取决于实例的生命周期。

### 常用算法排序

简单排序：冒泡排序、选择排序、插入排序(最快)

高级排序：快速排序、归并排序、希尔排序

相关算法知识：划分、递归、二分查找

```java
import java.util.Arrays;

/**
 * 参看url ： https://www.cnblogs.com/bjh1117/p/8335628.html
 * 冒泡排序，选择排序,插入排序
 */
public class TestStatic {
    public static void main(String[] args) {
        // 冒泡排序
        BubbleSort();
        // 选择排序
        selectSort();
        // 插入排序
        insertSort();
    }

    public static void BubbleSort() {
        int[] arr = {1, 5, 3, 4, 0};
        for (int i = 0; i < arr.length - 1; i++) {
            for (int j = i + 1; j < arr.length; j++) {
                if (arr[i] < arr[j]) {
                    int temp = arr[i];
                    arr[i] = arr[j];
                    arr[j] = temp;
                }
            }
        }
        System.out.println(Arrays.toString(arr));
    }

    public static void selectSort() {
        int[] arr = {1, 3, 2, 45, 65, 33, 12};
        // 做第i趟排序
        for (int i = 0; i < arr.length - 1; i++) {
            int k = i;
            // 选最小的记录
            for (int j = k + 1; j < arr.length; j++) {
                if (arr[j] < arr[k]) {
                    // 记下目前找到的最小值所在的位置
                    k = j;
                }
            }
            // 在内层循环结束，也就是找到本轮循环的最小的数以后，再进行交换
            if (i != k) {  //交换a[i]和a[k]
                int temp = arr[i];
                arr[i] = arr[k];
                arr[k] = temp;
            }
        }
        System.out.println(Arrays.toString(arr));
    }

    /**
     * 升序
     * 待比较数据：7, 6, 9, 8, 5,1
     * 　　第一轮：指针指向第二个元素6，假设6左面的元素为有序的，将6抽离出来，形成7,_,9,8,5,1，从7开始，6和7比较，发现7>6。将7右移，形成_,7,9,8,5,1，6插入到7前面的空位，结果：6,7,9,8,5,1
     * 　　第二轮：指针指向第三个元素9，此时其左面的元素6,7为有序的，将9抽离出来，形成6,7,_,8,5,1，从7开始，依次与9比较，发现9左侧的元素都比9小，于是无需移动，把9放到空位中，结果仍为：6,7,9,8,5,1
     * 　　第三轮：指针指向第四个元素8，此时其左面的元素6,7,9为有序的，将8抽离出来，形成6,7,9,_,5,1，从9开始，依次与8比较，发现8<9，将9向后移，形成6,7,_,9,5,1，8插入到空位中，结果为：6,7,8,9,5,1
     * 　　第四轮：指针指向第五个元素5，此时其左面的元素6,7,8,9为有序的，将5抽离出来，形成6,7,8,9,_,1，从9开始依次与5比较，发现5比其左侧所有元素都小，5左侧元素全部向右移动，形成_,6,7,8,9,1，将5放入空位，结果5,6,7,8,9,1。
     * 　　第五轮：同上，1被移到最左面，最后结果：1,5,6,7,8,9。
     */
    public static void insertSort() {
        int[] arr = {38, 65, 97, 76, 13, 27, 49};
        // 外层向右的index，即作为比较对象的数据的index
        for (int index = 1; index < arr.length; index++) {
            // 用作比较的数据
            int temp = arr[index];
            int leftindex = index - 1;
            // 当比到最左边或者遇到比temp小的数据时，结束循环
            while (leftindex >= 0 && arr[leftindex] > temp) {
                arr[leftindex + 1] = arr[leftindex];
                leftindex--;
            }
            // 把temp放到空位上
            arr[leftindex + 1] = temp;
        }
        System.out.println(Arrays.toString(arr));
    }
}
```

```
[5, 4, 3, 1, 0]
[1, 2, 3, 12, 33, 45, 65]
[13, 27, 38, 49, 65, 76, 97]
```

### 什么是跨域

![d3](https://volc1612.gitee.io/blog/images/interview-question/d3.png)

### 解决跨域问题方式的优缺点

#### JSONP的优缺点

JSONP的优点是：它不像XMLHttpRequest对象实现的Ajax请求那样受到同源策略的限制；它的兼容性更好，在更加古老的浏览器中都可以运行，不需要XMLHttpRequest或ActiveX的支持；并且在请求完毕后可以通过调用callback的方式回传结果。

JSONP的缺点则是：它只支持GET请求而不支持POST等其它类型的HTTP请求；它只支持跨域HTTP请求这种情况，不能解决不同域的两个页面之间如何进行JavaScript调用的问题。

#### 服务器代理ginx解决跨域问题

浏览器有跨域限制，但是服务器不存在跨域问题，所以可以由服务器请求所要域的资源再返回给客户端。
DomainA客户端（浏览器） ==> DomainA服务器 ==> DomainB服务器 ==> DomainA客户端（浏览器）

参考资料：https://www.cnblogs.com/gabrielchen/p/5066120.html

#### 后台添加过滤器彻底解决跨域问题

过滤器中设置响应头

```
resp.setHeader("Access-Control-Allow-Origin", "*");
```

### 过滤器

```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.context.annotation.Configuration;
import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Configuration
@WebFilter(urlPatterns = "/*", filterName = "testFilter")
@Slf4j
public class Filter0 implements Filter {
    @Override
    public void init(FilterConfig filterConfig) {
        log.info("过滤器0的init方法");
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain)
            throws ServletException, IOException {
        long start = System.currentTimeMillis();
        log.info("Filter0 Execute cost=" + (System.currentTimeMillis() - start));
        /**
         * 解决跨域和预请求
         */
        HttpServletRequest req = (HttpServletRequest) servletRequest;
        HttpServletResponse resp = (HttpServletResponse) servletResponse;
        resp.setHeader("Access-Control-Allow-Origin", "file://");
        // 允许所有的请求
        // resp.setHeader("Access-Control-Allow-Origin", "*");
        // 跨域 Header
        resp.setHeader("Access-Control-Allow-Methods", "*");
        resp.setHeader("Access-Control-Allow-Headers", "*");
        // 预请求
        resp.setHeader("Access-Control-Max-Age", "3600");
        filterChain.doFilter(req, resp);
        log.info("Filter0之后执行");
    }

    @Override
    public void destroy() {
        log.info("Filter0的destroy方法");
    }
}
```

#### html模拟跨域请求

```html
<!DOCTYPE html>
<html>
<meta charset="UTF-8">
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<script type="text/javascript" src="../static/jquery-3.3.1.min.js"></script>
<body>

<h1>ajax请求允许跨域</h1>
<script>

	$(function(){
		$.ajax({
            url: "http://localhost:8080/hello",
            type: "GET",
            dataType: "json",
            async: false,
            data: {},
            //  headers: {
            //   'token':'tokenvalue'
          	//  },
            success: function (data, status) {
            	console.log("成功");
            }, error: function (data) {
                console.log("错误");
            }
        });
	})
</script>
</body>
</html>
```

浏览器控制台报错：

```
jquery-3.3.1.min.js:2 Failed to load http://localhost:8080/hello: Response to preflight request doesn't pass access control check: The 'Access-Control-Allow-Origin' header has a value 'http://localhost:8080/hello' that is not equal to the supplied origin. Origin 'null' is therefore not allowed access.
```

![c](https://volc1612.gitee.io/blog/images/interview-question/d5.png)

Origin null是本地文件系统，因此这表明您正在加载通过file：// URL进行加载调用的HTML页面（例如，只需在本地文件浏览器或类似文件中双击它）。不同的浏览器采用不同的方法将相同来源策略应用到本地文件。Chrome要求比较严格，不允许这种形势的跨域请求。而最好使用http:// 访问html.

允许Chrom以File://形式请求有两种解决方式
第一种
首先修改Chrome快捷方式目标路径，添加" --allow-file-access-from-files"，有空格，重启浏览器。
![c](https://volc1612.gitee.io/blog/images/interview-question/d6.png)
再设置后台过滤器响应头

```java
resp.setHeader("Access-Control-Allow-Origin", "file://");
```

第二种

再设置后台过滤器响应头

```java
resp.setHeader("Access-Control-Allow-Origin", "*");
```

#### WebSocket

WebSocket protocol 是HTML5一种新的协议。它实现了浏览器与服务器全双工通信，同时允许跨域通讯，是server push技术的一种很棒的实现

### TreeSet中元素类型必须实现Comparable

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Set;
import java.util.TreeSet;

public class TestUser implements Comparable<TestUser> {

    private String name;
    private int age;

    public TestUser(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public int compareTo(TestUser o) {
        return this.age - o.age;
    }

    @Override
    public String toString() {
        return "TestUser{" +
                "age=" + age +
                ", name='" + name + '\'' +
                '}';
    }

    public static void main(String[] args) {
        Set<TestUser> set = new TreeSet<>();
        // List<TestUser> set = new ArrayList<>();
        set.add(new TestUser("hh", 23));
        set.add(new TestUser("aa", 20));
        set.add(new TestUser("zz", 27));
        System.out.println(set.toString());
    }
}

```
结果

```
[TestUser{age=20, name='aa'}, TestUser{age=23, name='hh'}, TestUser{age=27, name='zz'}]
```

如果不实现comparable接口会报错

```
Exception in thread "main" java.lang.ClassCastException: test.TestUser cannot be cast to java.lang.Comparable
```

那为什么回报这个错呢，原因是TreeSet使用TreeMap来实现的，它的排序依靠元素的compareTo方法，只有将类转化成Comparable(接口)类，才能调用Comparable中的compareTo方法进行比较。
而包装类如Integer,Double都已经实现了Comparable接口(public final class Integer extends Number implements Comparable<Integer>)，所以会自动排序。

### 为什么实现Serializbale接口就能够进行序列化？

```java

public class Parent implements Serializable {

    private static final long serialVersionUID = 1234L;
    
    public Parent1(String name,int age){
        this.name = name;
        this.age = age;
    }
    
    private String name;
    private int age;

    
    public String toString(){
        return "Parent:"+name+" "+age;
    }
    
}
```

测试类

```java
public class TestSeri {
    
    public static  void seri(Parent parent){
        try {
            FileOutputStream fo = new FileOutputStream("src/est.txt");
            ObjectOutputStream oos = new ObjectOutputStream(fo);
            oos.writeObject(parent);
            oos.flush();
            oos.close();
        } catch (Exception e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }        
    }
    
    public static void main(String[] args){
        Parent parent = new Parent("ouym",24);
        seri(parent);
                
    }
```

序列话的过程主要有三步：

（1）新建文件输出流用来指定一个位置存储序列化的内容。

（2）新建一个ObjectOutputStream对象oos

（3）序列化：调用writeObject(parent)方法。

关键看第三部writeObject的内部实现过程：

```java
public final void writeObject(Object obj) throws IOException {
    //省略部分代码
    try {
        // 调用writeObject0()方法序列化
        writeObject0(obj, false);
    } catch (IOException ex) {
        if (depth == 0) {
            writeFatalException(ex);
        }
        throw ex;
    }
}
```

可见writeObject方法的主要实现过程交给了writeObject0(obj，false)方法。

```java
private void writeObject0(Object obj, boolean unshared)
    throws IOException
{
    // 一些省略代码
    try {
        // 一些省略代码，其他的细节我们不深入
        // remaining cases
        if (obj instanceof String) {
            writeString((String) obj, unshared);
        } else if (cl.isArray()) {
            writeArray(obj, desc, unshared);
        } else if (obj instanceof Enum) {
            writeEnum((Enum) obj, desc, unshared);
        } else if (obj instanceof Serializable) {
            // 被序列化对象实现了Serializable接口
            writeOrdinaryObject(obj, desc, unshared);
        } else {
            if (extendedDebugInfo) {
                throw new NotSerializableException(
                    cl.getName() + "\n" + debugInfoStack.toString());
            } else {
                throw new NotSerializableException(cl.getName());
            }
        }
    } finally {
        depth--;
        bout.setBlockDataMode(oldMode);
    }
}
```

从上述代码第8行开始，我们知道String，数组和枚举类型可以直接序列化（不难猜出String和Enum类实现了Serializbale接口）。若不是上述三种类型的话，接下来重点看14到16行代码，如果对象实现了Serializbale接口的话，就用writeOrdinaryObject（）方法进行序列化操作。这里我们就不看writeOrdinaryObject方法的细节了，因为我们已经找到了标题的答案。Serializable接口这是一个标识，告诉程序所有实现了他的对象都可以进行序列化。

### java高效解析大文件方案

参考： http://www.importnew.com/14512.html

### java中几种线程池的介绍

https://www.cnblogs.com/aaron911/p/6213808.html

### 聚集索引和非聚集索引

聚集索引基于数据行的键值在表内排序和存储这些数据行。每个表只能有一个聚集索引，因为数据行本身只能按一个顺序存储。
使用聚集索引就是对这堆记录 进行 堆划分。即主要描述的是物理上的存储。
非聚集索引，也可以从生活中找到映射。非聚集索引强调的是逻辑分类。可以说是定义了一套存储规则，而需要有一块控件来维护这个规则，这个被称之为索引表。

区别：

       聚集索引：可以帮助把很大的范围，迅速减小范围。但是查找该记录，就要从这个小范围中Scan了。 
       非聚集索引：把一个很大的范围，转换成一个小的地图。你需要在这个小地图中找你要寻找的信息的位置。然后通过这个位置，再去找你所需要的记录

### http关闭缓存

```java
//==通过设置请求头对响应禁用缓存
resp.setHeader("Cache-Control", "no-store");//or no-cache
resp.setHeader("Pragrma", "no-cache"); 
resp.setDateHeader("Expires", 0);
```

### 字符串首字母大小写转换

```java
//首字母转小写
public static String toLowerCaseFirstOne(String s){
  if(Character.isLowerCase(s.charAt(0)))
    return s;
  else
    return (new StringBuilder()).append(Character.toLowerCase(s.charAt(0))).append(s.substring(1)).toString();
}
//首字母转大写
public static String toUpperCaseFirstOne(String s){
  if(Character.isUpperCase(s.charAt(0)))
    return s;
  else
    return (new StringBuilder()).append(Character.toUpperCase(s.charAt(0))).append(s.substring(1)).toString();
}
```

效率最高

```java
// 进行字母的ascii编码前移，效率要高于截取字符串进行转换的操作
    public static void main(String[] args) {
        String str = "wggggggg";
        char[] cs = str.toCharArray();
        cs[0] -= 32;
        System.out.println(String.valueOf(cs));
    }
```

### 什么是请求超时

超时分为请求超时和响应超时
**请求超时**，比如现在网络超级不好，当客户端发起一个请求，通信层开始请求与服务器建立连接（包括在重试），如果在5S之内还没有连接到服务器，那么认为超时。
**响应超时**，当我们连接到服务器时，一般比如url参数（url?key=value）会直接提交到服务器，比如body类型的参数（Form、JsonBody、key=value&key1=value1等）我们会通过连接中的stream再手动写出去，当服务器接受到请求数据后开始【处理数据->响应】，这个【处理数据->响应】阶段就可能会发生响应超时，比如服务器去执行数据库操作，在5S内还没有对stream做出反馈，那么客户端就认为超时（少部分人对下载有误解，下载则不一样，因为一直有输出数据，也就是对stream做了反馈），主动断开和服务器的连接。以上两种情况，底层一般都是抛出一个TimeoutException的异常。


### 搜索添加索引防止重复请求接口

需求： 搜索时需要通过索引引擎取数据，如果索引引擎中没有数据需要添加到所引擎中，再返回数据。这时由于添加索引引擎中需要时间，用户可能会重复点击搜索，出现重复请求这种情况会导致重复添加索引，影响效率？如何解决

思路：请求进行处理时用redis锁标识上一请求是否处理完成，如果处理完成，则通过索引去数据返回，速度很快。如果正在执行，则使用死循环等待索引建好之后，再从索引中取数据返回。

### 非静态代码块，静态成员，静态代码块，构造方法加载顺序

```java
public class TestStatic3 {
    public static void main(String[] args) {
    }
    {
        System.out.println("TestStatic3->非静态代码块");
    }
    static {
        System.out.println("TestStatic3->静态代码块");
    }

    public TestStatic3() {
        System.out.println("TestStatic3->构造方法");
    }
}

```

运行该类的main方法时，JVM首先加载该类的静态代码块。
```
TestStatic3->静态代码块
```

```java
public class TestStatic3 {
    public static void main(String[] args) {
    }
    static TestStatic3 st = new TestStatic3();
    {
        System.out.println("TestStatic3->非静态代码块");
    }
    static {
        System.out.println("TestStatic3->静态代码块");
    }
    public TestStatic3() {
        System.out.println("TestStatic3->构造方法");
    }
}
```

该类的加载顺序：
1. 静态成员变量`st`，在`new TestStatic3`时则遵循JVM创建对象的加载顺序，非静态成员变量->非静态代码块->构造方法->静态成员变量->静态代码块；
2. 非静代码块；
3. 构造方法；
4. 该类的静态代码块；

```
TestStatic3->非静态代码块
TestStatic3->构造方法
TestStatic3->静态代码块
```

```java
package com.redstar.imp.entity.po;

class A {
    public A() {
        System.out.println("class A");
    }

    {
        System.out.println("I'm A class");
    }

    static {
        System.out.println("class A static");
    }
}

public class B extends A {
    public B() {
        System.out.println("class B");
    }

    {
        System.out.println("I'm B class");
    }

    static {
        System.out.println("class B static");
    }

    public static void main(String[] args) {
        new B();
    }
}
```

先加载A和B的静态成员变量->静态代码块->然后执行main方法new 对象过程，A的非静态代码块->A的构造->B的非静态代码块->B的构造

```
class A static
class B static
I'm A class
class A
I'm B class
class B
```

```java
public class TestStatic {
    public static void main(String[] args) {
    }
    static TestStatic st = new TestStatic();
    static {
        System.out.println("A1");
    }
    {
        System.out.println("A2");
    }
    TestStatic() {
        System.out.println("A3");
        System.out.println("Aa = " + a + ", Ab = " + b);
    }

    public static void staticFunction() {
        System.out.println("A4");
    }
    int a = 110;
    static int b = 120;
}
```

new 一个对象时JVM加载顺序，非静态成员变量->非静态代码块->构造方法->静态成员变量->静态代码块

```
A2
A3
Aa = 110, Ab = 0
A1
```

```java
class Fu2 {
    static int fuStaticVariable = 3;
    int fuVariable = 4;

    public Fu2() {
        System.out.println(fuVariable);
        System.out.println(fuStaticVariable);
        System.out.println("Fu-构造");
    }

    {
        System.out.println(fuVariable);
        System.out.println(fuStaticVariable);
        System.out.println("Fu-非静态代码块");
    }

    static {
        System.out.println(fuStaticVariable);
        System.out.println("Fu-静态代码块");
    }
}

public class Zi2 extends Fu2 {

    static {
        new Fu2();
        System.out.println("Zi-静态代码块");
    }

    static Fu2 fu2 = new Fu2();

    public Zi2() {
        System.out.println(ziVariable);
        System.out.println(fu2);
        System.out.println("Zi-构造");
    }

    {
        System.out.println(fu2);
        System.out.println("Zi-非静态代码块");
    }

    int ziVariable = 1;

    public static void main(String[] args) {
        new Zi2();
    }

    /**
     * 1. 加载 fuStaticVariable = 3
     * 2. 3
     * 3. Fu-静态代码块
     * 4. 加载 fuVariable = 4
     * 5. 4
     * 6. 3
     * 7. Fu-非静态代码块
     * 8. 4
     * 9. 3
     * 10. Fu-构造
     * 11. Zi-静态代码块
     * 12. 4
     * 13. 3
     * 14. Fu-非静态代码块
     * 15. 4
     * 16. 3
     * 17. Fu-构造
     * 18. 4
     * 19. 3
     * 20. Fu-非静态代码块
     * 21. 4
     * 22. 3
     * 23. Fu-构造
     * 24. fu2对象
     * 25. Zi-非静态代码块
     * 26. 加载 ziVariable = 1
     * 27. 1
     * 28. fu2对象
     * 29. Zi-构造
     */
}
```

总结
```
1. 父类优于子类
2. 静态优于非静态
3. 非静态优于构造
4. 只有new对象或new子对象才会加载父非静态成员变量和非静态方法，最后加载构造
```

### 蚂蚁金服面试题之服务器性能指标

**简介：Linux服务性能指标讲解**

* 为什么要观察服务器性能指标



![interview-question-a](https://volc1612.gitee.io/blog/images/interview-question/interview-question-a.png)

* load average
  * 为什么会有三个数字呢？它们的意思分别是1分钟、5分钟、15分钟内系统的平均负荷。
  * 当CPU完全空闲的时候，平均负荷为0,值越低系统负荷越低
  * 值跟CPU核数相关，比如8核CPU，最高负载为8.0
  * grep -c 'model name' /proc/cpuinfo"命令，直接返回CPU的总核心数

* mem
  * 展示了当前内存的状态，total是总的内存大小，userd是已使用的，free是剩余的，buffers是目录缓存
* Task
  * 展示了目前的进程总数及所处状态，要注意zombie，表示僵尸进程，不为0则表示有进程出现问题
* Cpu
* * 展示了当前CPU的状态，us表示用户进程占用CPU比例，sy表示内核进程占用CPU比例，id表示空闲CPU百分比，wa表示IO等待所占用的CPU时间的百分比。**wa占用超过30%则表示IO压力很大**。

### spring security工作原理

### @Autowired什么时候注入对象

### 关闭线程关闭线程池

### 线程池的种类

### spring中bean的生命周期

### sleep(),join(), yield()区别

### CountDownLatch原理

### volatile关键字的作用

### 反射的用途和实现