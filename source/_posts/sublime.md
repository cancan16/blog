---
title: sublime
date: 2018-10-22 20:56:40
update: 2018-10-24 20:56:40
categories: SUBLIME
tags: [sublime]
---

![b](https://volc1612.gitee.io/blog/images/sublime/b.png)

<!-- more -->

### sublime安装常用插件

#### Package Control

1. Ctrl+ \` 调出console

2. 输入以下内容回车(在线安装)

```
import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
```

安装成功重启sublime

#### 安装emmet插件

一种快速编写html/css的方法，可补全html5。使用Package Control安装插件

Ctrl + Shift + P 打开命令面板输入"package control install package"回车会出现安装package的面板。搜索emmet并安装。
Ctrl + N 新建编辑窗口，在右下角点击Plain Text选择HTML，在新窗口输入!，按tab键生成html5。

#### 安装HTML-CSS-JS Prettify插件格式化代码

1、ctrl + shift + p 打开控制台
2、输入install package，选择install package（如果已经安装过install package略过）
3、搜索输入：HTML-CSS-JS Prettify，并安装该插件
4、重启sublime

代码编辑窗口右击选择`HTTP/CSS/JS prettify->prettify Code`格式化代码。

参考：https://www.cnblogs.com/xuliangxing/p/7995427.html

#### sublimez菜单栏复制粘贴文件插件SideBarEnhancements

搜索SideBarEnhancements点击安装

#### 删除插件

Ctrl+Shift+P调出命令面板，输入remove，调出Remove Package

#### SublimeLinter

Sublime Text 3使用SublimeLinter配置JS,CSS,HTML语法检查。

##### 安装nodejs

安装SublimeLinter前必须安装<a href="https://nodejs.org/zh-cn/">node.js</a>。

##### 安装SublimeLinter

> 打开Sublime，按下 Ctrl+Shift+p 进入 Command Palette;

> 输入install进入 Package Control: Install Package;

> 输入SublimeLinter，选择SublimeLinter进行安装。

##### 安装SublimeLinter-jshint

> 打开Sublime，按下 Ctrl+Shift+p 进入 Command Palette;

> 输入install进入 Package Control: Install Package;

> 输入SublimeLinter-jshint，选择SublimeLinter-jshint进行安装。

##### 安装jshint

命令: npm install -g jshint

```
C:\Users\Administrator>npm install -g jshint
C:\Users\Administrator\AppData\Roaming\npm\jshint -> C:\Users\Administrator\AppData\Roaming\npm\node_modules\jshint\bin\jshint

> phantomjs-prebuilt@2.1.16 install C:\Users\Administrator\AppData\Roaming\npm\node_modules\jshint\node_modules\phantomjs-prebuilt
> node install.js

PhantomJS not found on PATH
Downloading https://github.com/Medium/phantomjs/releases/download/v2.1.1/phantomjs-2.1.1-windows.zip
Saving to C:\Users\ADMINI~1\AppData\Local\Temp\phantomjs\phantomjs-2.1.1-windows.zip
Receiving...
  [=======================================-] 98%
Received 17767K total.
Extracting zip contents
Removing C:\Users\Administrator\AppData\Roaming\npm\node_modules\jshint\node_modules\phantomjs-prebuilt\lib\phantom
Copying extracted folder C:\Users\ADMINI~1\AppData\Local\Temp\phantomjs\phantomjs-2.1.1-windows.zip-extract-1540297542355\phantomjs-2.1.1-windows -> C:\Users\Administrator\AppData\Roaming\npm\node_modules\jshint\node_modules\phantomjs-prebuilt\lib\phantom
Writing location.js file
Done. Phantomjs binary available at C:\Users\Administrator\AppData\Roaming\npm\node_modules\jshint\node_modules\phantomjs-prebuilt\lib\phantom\bin\phantomjs.exe
+ jshint@2.9.6
added 119 packages in 323.275s

C:\Users\Administrator>jshint -v
jshint v2.6.3
```

![a](https://volc1612.gitee.io/blog/images/sublime/a.png)

#### markdown实时预览

OmniMarkupPreviewer

安装后在首选项->Package Setting里修改OmniMarkupPreviewer的setting-user添加

```
{
    "renderer_options-MarkdownRenderer": {
        "extensions": ["tables", "fenced_code", "codehilite"]
    }
}
```

在要预览的文件里`右键->Preview markup in browser`或者按快捷键`Ctrl + Alt + O`就可以在浏览器里预览了。

---

# 讲了这么多，然而已入坑[`Visual Studio Code`][1]。






[1]: https://code.visualstudio.com/