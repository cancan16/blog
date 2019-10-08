---
title: Hexo之NexT主题搭建博客详细过程
date: 2016-10-03 15:39:31
update: 2017-03-24 10:30:10
categories: HEXO
tags: [nodejs, hexo, next]
---
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=186513&auto=0&height=66"></iframe>
<!-- more -->
本站是用hexo来搭建的，使用的是next主题。这里记录一下搭建的整个过程，仅供参考！有什么问题请留言通知，谢谢支持！
### 更新内容 2017-03-24

>* 使用友言评论功能
>* 解析域名

***

### 环境准备
下载安装nodejs和git(for window)，声明一下我的搭建环境win764
#### nodejs安装
官网下载地址：https://nodejs.org/en/ 下载v*** LTS Recommended For Most Users
>设置系统变量      


我把nodejs安装在c盘program files的nodejs目录下  
编辑系统环境变量path的值，追加C:\Program Files\nodejs
> 安装Express开发框架  

以管理员打开cmd命令窗口，执行命令
```apache
npm install -g express
npm install -g express-generator
```
在C:\Program Files\nodejs\node_modules中会生成express和express-generator两个文件夹表示安装好了
> 新建项目  

进入C:\Program Files\nodejs，新建名称为newproject的项目
```apache
cd C:\Program Files\nodejs
express -t ejs newsproject
```
新建成功在nodesj目录下会生成newproject目录，其目录下大致有以下文件
```
bin          相关运行脚本
public       静态资源
routes       路由表
views        试图模板  
app.js       视图文件夹  
packge.json  项目依赖说明
```
> 关于执行npm install -g express命令的出现错误的解决方式  

```apache
npm config list # 查看以前修改过的命令，针对错误的地方删除掉。
```
删除代理
```apache
npm config delete proxy
```
删除注册的目录  
```apache
npm config delete registry
```
npm仓库是国外服务器，经常连接不上或安装慢，这里推荐使用 淘宝 NPM 镜像 来安装，设置镜像  
```apache
npm config set registry https://registry.npm.taobao.org 
```
查看是否修改成功  
```apache
npm config list
```
再重新安装express模块  
```apache
npm install -g express
```

>安装模块  

进入newproject目录, 安装模块
```apache
cd newsproject
npm install  
```
>启动项目  

```apache
npm start
```
在浏览器访问http://127.0.0.1:3000/ 测试出现下面图视表示nodejs安装成功，并可以正常部署项目
![测试](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/result.png)  
可输入以下命令查看版本号
```apache
node -v
npm -v
```
#### git安装
官网下载地址：https://git-scm.com/downloads/ 这个就不多说了
### 安装Hexo
git安装成功后在桌面右击选择git bash here, 命令窗口
![desktop](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/desktop.gif)


>安装hexo  

执行命令

```apache
npm i -g hexo  # g代表全局,npm默认为当前项目安装
```
在D盘新建blog文件夹，这个文件夹就是博客的根目录，打开blog空白处右击选择git bash here执行以下命令。首先对blog文件夹进行hexo初始化
```apache
hexo init
```
会生成一些文件
>安装依赖包

```apache
npm install
```
可能会遇到的错误
```apache
npm ERR! registry error parsing json # 错误
```
可能需要设置npm代理,执行命令
```apache
npm config set registry http://registry.cnpmjs.org
```
```apache
hexo:command not found
```
删除刚刚安装的C:\Users\Administrator\AppData\Roaming\npm目录，重新执行命令npm install -g hexo安装hexo

>生成静态资源

生成public文件夹(浏览器访问资源)
```apache
hexo g
```
>启动服务

```apache
hexo s
```
在浏览器访问：http://localhost:4000/ 便可查看hexo默认的主题
![hexo](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/h.png)

>clone NexT主题

在blog根目录执行命令
```apache
git clone https://github.com/iissnan/hexo-theme-next themes/next
```
clone完成后打开blog下的themes文件夹就可以看到有两个主题，一个是默认的，一个是刚刚clone的NexT主题  
站点配置文件D:\blog\\_config.yml  
主题配置文件D:\blog\themes\next\\_config.yml  
在站点配置文件_config.yml中进行搜索key为'theme'，如果没有theme就添加key为theme，其值为next  
```
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
# theme: landscape
# 设置主题为next
theme: next
```
需要注意的是冒号后面有一个空格
在blog目录中右击选择git bash here 分别执行清除静态资源，生成静态资源，启动服务命令
```apache
hexo clean
hexo g
hexo s
```
在浏览器中访问http://localhost:4000/ 便可看到效果


打开主题配置文件_config.yml找到Schemes，如下所示有三种方案可选，只需将#去掉就可以了，可以都尝试一下，选择喜欢的风格，修改之后不用重启服务直接刷新浏览器就能看到效果
```
# 切换样式
# Schemes
#scheme: Muse
#scheme: Mist
scheme: Pisces
```
到此本地资源基本上完成了，下面贴出站点配置_config.yml和主题配置_config.yml里面注释了信息**仅供**参考
#### 站点配置文件_config.yml

```
# Hexo Configuration
## Docs: https://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site
title: Blog  # 首页title名称
subtitle: Volc's Blog  
description: volc #作者简介
author: VOLC # 作者名称
language: zh-Hans # 修改语言环境为简体汉字
timezone:

# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: https://volc1612.gitee.io/blog/
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:

# Directory
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# Writing
new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
future: true
highlight:
  enable: true
  line_number: true
  auto_detect: false
  tab_replace:

# Category & Tag
default_category: uncategorized
category_map:
  编程: programming
  生活: life
  其他: other
tag_map:

# Date / Time format
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss

# Pagination
## Set per_page to 0 to disable pagination
per_page: 10
pagination_dir: page

# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
#theme: landscape
# 设置主题为next
theme: next

# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repository: git@github.com:volc1605/volc1612.gitee.io/blog.git
  branch: master

# 设置头像
avatar: /images/0.png

# Social links
social:
  GitHub: https://github.com/volc1605
  Twitter: https://twitter.com/volcliu
  微博: http://weibo.com/2297464654/profile?topnav=1&wvr=6&is_all=1
  知乎: https://www.zhihu.com/people/volc

# hexo sitemap网站地图
sitemap:
  path: sitemap.xml
baidusitemap:
  path: baidusitemap.xml

search:
  path: search.xml
  field: post
# 百度站点统计
baidu_analytics: 87980c**************99ec5e26fb5
# google统计
google_analytics: 85*****1
```
#### 主题配置文件_config.yml
```
# ---------------------------------------------------------------
# Site Information Settings
# ---------------------------------------------------------------

# Put your favicon.ico into `hexo-site/source/` directory.
favicon: /favicon.ico

# Set default keywords (Use a comma to separate)
keywords: "Hexo, NexT"

# Set rss to false to disable feed link.
# Leave rss as empty to use site's feed link.
# Set rss to specific value if you have burned your feed already.
rss:

# Specify the date when the site was setup
#since: 2015

# Canonical, set a canonical link tag in your hexo, you could use it for your SEO of blog.
# See: https://support.google.com/webmasters/answer/139066
# Tips: Before you open this tag, remeber set up your URL in hexo _config.yml ( ex. url: http://yourdomain.com )
canonical: true


# ---------------------------------------------------------------
# Menu Settings
# ---------------------------------------------------------------

# When running the site in a subdirectory (e.g. domain.tld/blog), remove the leading slash (/archives -> archives)
menu:
  home: /
  categories: /categories
  archives: /archives
  tags: /tags
  message: /message
  # search: /search
  about: /about
  #commonweal: /404.html


# Enable/Disable menu icons.
# Icon Mapping:
#   Map a menu item to a specific FontAwesome icon name.
#   Key is the name of menu item and value is the name of FontAwsome icon. Key is case-senstive.
#   When an question mask icon presenting up means that the item has no mapping icon.
menu_icons:
  enable: true
  #KeyMapsToMenuItemKey: NameOfTheIconFromFontAwesome
  home: home
  categories: th
  tags: tags
  archives: archive
  commonweal: heartbeat
  message: external-link
  # search: search
  about: user




# ---------------------------------------------------------------
# Scheme Settings
# ---------------------------------------------------------------

# 切换样式
# Schemes
#scheme: Muse
scheme: Mist
#scheme: Pisces


# ---------------------------------------------------------------
# Font Settings
# - Find fonts on Google Fonts (https://www.google.com/fonts)
# - All fonts set here will have the following styles:
#     light, light italic, normal, normal intalic, bold, bold italic
# - Be aware that setting too much fonts will cause site running slowly
# - Introduce in 5.0.1
# ---------------------------------------------------------------
font:
  enable: true

  # Uri of fonts host. E.g. //fonts.googleapis.com (Default)
  host:

  # Global font settings used on <body> element.
  global:
    # external: true will load this font family from host.
    external: true
    family: Lato

  # Font settings for Headlines (h1, h2, h3, h4, h5, h6)
  # Fallback to `global` font settings.
  headings:
    external: true
    family:

  # Font settings for posts
  # Fallback to `global` font settings.
  posts:
    external: true
    family:

  # Font settings for Logo
  # Fallback to `global` font settings.
  # The `size` option use `px` as unit
  logo:
    external: true
    family:
    size: 33

  # Font settings for <code> and code blocks.
  codes:
    external: true
    family:




# ---------------------------------------------------------------
# Sidebar Settings
# ---------------------------------------------------------------


# Social Links
# Key is the link label showing to end users.
# Value is the target link (E.g. GitHub: https://github.com/iissnan)
#social:
  #LinkLabel: Link


# Social Links Icons
# Icon Mapping:
#   Map a menu item to a specific FontAwesome icon name.
#   Key is the name of the item and value is the name of FontAwsome icon. Key is case-senstive.
#   When an globe mask icon presenting up means that the item has no mapping icon.
social_icons:
  enable: true
  # Icon Mappings.
  # KeyMapsToSocalItemKey: NameOfTheIconFromFontAwesome
  GitHub: github
  Twitter: twitter
  微博: weibo


# Sidebar Avatar
# in theme directory(source/images): /images/avatar.jpg
# in site  directory(source/uploads): /uploads/avatar.jpg
#avatar:


# Table Of Contents in the Sidebar
toc:
  enable: true

  # Automatically add list number to toc.
  number: true


# Creative Commons 4.0 International License.
# http://creativecommons.org/
# Available: by | by-nc | by-nc-nd | by-nc-sa | by-nd | by-sa | zero
#creative_commons: by-nc-sa
#creative_commons:


sidebar:
  # Sidebar Position, available value: left | right
  position: left
  #position: right

  # Sidebar Display, available value:
  #  - post    expand on posts automatically. Default.
  #  - always  expand for all pages automatically
  #  - hide    expand only when click on the sidebar toggle icon.
  #  - remove  Totally remove sidebar including sidebar toggler.
  display: post
  #display: always
  #display: hide
  #display: remove


# Blogrolls
links_title: 友情链接
links_layout: inline
links_icon: link  # 设置图标
links:
  一俗人: http://yisu.ren/index.php/




# ---------------------------------------------------------------
# Misc Theme Settings
# ---------------------------------------------------------------

# Custom Logo.
# !!Only available for Default Scheme currently.
# Options:
#   enabled: [true/false] - Replace with specific image
#   image: url-of-image   - Images's url
custom_logo:
  enabled: false
  image:


# Code Highlight theme
# Available value:
#    normal | night | night eighties | night blue | night bright
# https://github.com/chriskempson/tomorrow-theme
highlight_theme: night eighties


# Automatically scroll page to section which is under <!-- more --> mark.
scroll_to_more: true


# Automatically Excerpt. Not recommand.
# Please use <!-- more --> in the post to control excerpt accurately.
auto_excerpt:
  enable: true  # 开启文章阅全文
  length: 2000   # 显示长度


# Wechat Subscriber
#wechat_subscriber:
  #enabled: true
  #qcode: /path/to/your/wechatqcode ex. /uploads/wechat-qcode.jpg
  #description: ex. subscribe to my blog by scanning my public wechat account




# ---------------------------------------------------------------
# Third Party Services Settings
# ---------------------------------------------------------------

# MathJax Support
mathjax:
  enable: false
  cdn: //cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML


# Swiftype Search API Key
#swiftype_key:

# Baidu Analytics ID
#baidu_analytics:
baidu_tongji: true

# Duoshuo ShortName
duoshuo_shortname: *******  # 开启多说评论功能

# Disqus
#disqus_shortname:

# Baidu Share
# Available value:
#    button | slide
#baidushare: true #
##  type: button

# Share
#jiathis:
#add_this_id:

# Share
duoshuo_share: true  # 开启多说的文章分享功能

# Google Webmaster tools verification setting
# See: https://www.google.com/webmasters/
#google_site_verification:


# Google Analytics
#google_analytics:

# CNZZ count
#cnzz_siteid:


# Make duoshuo show UA
# user_id must NOT be null when admin_enable is true!
# you can visit http://dev.duoshuo.com get duoshuo user id.
duoshuo_info:
  ua_enable: true
  admin_enable: false
  user_id: 0
  #admin_nickname: Author


# Facebook SDK Support.
# https://github.com/iissnan/hexo-theme-next/pull/410
facebook_sdk:
  enable: false
  app_id:       #<app_id>
  fb_admin:     #<user_id>
  like_button:  #true
  webmaster:    #true

# Facebook comments plugin
# This plugin depends on Facebook SDK.
# If facebook_sdk.enable is false, Facebook comments plugin is unavailable.
facebook_comments_plugin:
  enable: false
  num_of_posts: 10  # min posts num is 1
  width: 100%       # default width is 550px
  scheme: light     # default scheme is light (light or dark)


# Show number of visitors to each article.
# You can visit https://leancloud.cn get AppID and AppKey.
leancloud_visitors:
  enable: true
  app_id: uSB1*************************Hsz
  app_key: 98i************************aI5

# Show PV/UV of the website/page with busuanzi.
# Get more information on http://ibruce.info/2015/04/04/busuanzi/
busuanzi_count:
  # count values only if the other configs are false
  enable: false
  # custom uv span for the whole site
  site_uv: true
  site_uv_header: <i class="fa fa-user"></i>
  site_uv_footer:
  # custom pv span for the whole site
  site_pv: true
  site_pv_header: <i class="fa fa-eye"></i>
  site_pv_footer:
  # custom pv span for one page only
  page_pv: true
  page_pv_header: <i class="fa fa-file-o"></i>
  page_pv_footer:

# Tencent analytics ID
# tencent_analytics:

# Enable baidu push so that the blog will push the url to baidu automatically which is very helpful for SEO
baidu_push: false



#! ---------------------------------------------------------------
#! DO NOT EDIT THE FOLLOWING SETTINGS
#! UNLESS YOU KNOW WHAT YOU ARE DOING
#! ---------------------------------------------------------------

# Motion
use_motion: true

# Fancybox
fancybox: true


# Script Vendors.
# Set a CDN address for the vendor you want to customize.
# For example
#    jquery: https://ajax.googleapis.com/ajax/libs/jquery/2.2.0/jquery.min.js
# Be aware that you should use the same version as internal ones to avoid potential problems.
vendors:
  # Internal path prefix. Please do not edit it.
  _internal: vendors

  # Internal version: 2.1.3
  jquery:

  # Internal version: 2.1.5
  # http://fancyapps.com/fancybox/
  fancybox:
  fancybox_css:

  # Internal version: 1.0.6
  # https://github.com/ftlabs/fastclick
  fastclick:

  # Internal version: 1.9.7
  # https://github.com/tuupola/jquery_lazyload
  lazyload:

  # Internal version: 1.2.1
  # http://VelocityJS.org
  velocity:

  # Internal version: 1.2.1
  # http://VelocityJS.org
  velocity_ui:

  # Internal version: 0.7.9
  # https://faisalman.github.io/ua-parser-js/
  ua_parser:

  # Internal version: 4.4.0
  # http://fontawesome.io/
  fontawesome:


# Assets
css: css
js: js
images: images

# Theme version
version: 5.0.1

# Donate 文章末尾显示打赏按钮
reward_comment: 如果文章对您有用请随意打赏，谢谢支持！
wechatpay: /images/w.png
alipay: /images/z.jpg

```
### 菜单栏
#### 菜单栏为中文简体字
站点配置文件
```
language: zh-Hans
```
这样就引用了themes\next\languages\zh-Hans.yml文件，打开这个文件就可以看每个key对应的都是中文字体
#### 标签和分类
在主题配置文件中找到menu，如果想隐藏菜单栏中的某个选项只要在前面加上 # 即可
```
menu:
  home: /
  categories: /categories # 分类
  archives: /archives     # 归档
  tags: /tags             # 标签
  message: /message       # 留言
  about: /about           # 关于
  # commonweal: /404.html # 公益
```
我们将categories 和tags 设为显示
再找到menu_icons, 这里是每一个菜单选项前面对应的小图标
![ioc](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/ioc.png)

```
menu_icons:
  enable: true
  #KeyMapsToMenuItemKey: NameOfTheIconFromFontAwesome
  home: home
  categories: th
  tags: tags
  archives: archive
  commonweal: heartbeat
  message: external-link
  about: user
```
我们将categories 和tags 设为显示  
在blog文件夹中进行git bash here 执行以下命令生成categories 和 tags的页面
```
hexo n page "categories"
hexo n page "tags"
```
会生成source\categories 和 source\tags 两个文件夹，里面都有index.md文件.修改内容为
```
---
title: categories
type: categories
---
```
```
---
title: tags
type: tags
---
```
再使用命令生成博文文件
```
hexo n "name"    # name 文章名称
```
生成文章时，在对应的name.md中可以这样添加标签和分类
```
---
title: Hexo之NexT主题搭建博客详细过程
date: 2016-10-03 15:39:31
update: 2016-10-04 06:19:11
categories: hexo             # 分类
tags: [nodejs, hexo, NexT]   # [标签1, 标签2..., 标签n]
---
```
#### 新增留言功能
主题配置文件
```
menu:
  home: /
  categories: /categories
  archives: /archives
  tags: /tags
  message: /message       # 新增 message
  about: /about
  #commonweal: /404.html
```
配置图标
```
menu_icons:
  enable: true
  home: home
  categories: th
  tags: tags
  archives: archive
  commonweal: heartbeat
  message: external-link  # 新增 message
  about: user
```
配置对应的中文名称，在themes\next\languages\zh-Hans.yml文件中修改如下
```
menu:
  home: 首页
  archives: 归档
  categories: 分类
  tags: 标签
  about: 关于
  search: 搜索
  message: 留言            # 新增 留言
  commonweal: 公益404
  udpate: 更新
```
然后执行如下命令
```apache
hexo n page "message"
```
重新清除，生成和启动便可看到效果，只不过留言功能什么也没有

##### 引入多说评论功能
进入官网http://duoshuo.com/ 进行登录，建议不要使用微信进行登录，容易出现Scope 参数错误或没有 Scope 权限的问题。登录完成在首页进入后台管理，在设置里面包含：域名：zhangsan.duoshuo.com
在主题配置文件中
修改如下, duoshuo_shortname的值就是域名中的zhangsan
```
duoshuo_shortname: zhangsan  # 开启多说评论功能
# Share
duoshuo_share: true          # 开启多说的文章分享功能

```
留言功能已完成  
去掉分类和其他的选项的留言功能，只需修改index.md
在source\categories\index.md 中修改如下
```
---
title: categories
type: categories
comments: false       # 去掉评论功能
date: 2016-10-01 21:07:35
---
```
添加留言邮件提醒功能请参阅：http://www.tuicool.com/articles/iEN7riZ

#### 修改头像
设置菜单栏头像  
	把头像0.jpg图片放在themes\next\source\images中  
	在站点的_config.yml，修改字段 avatar
```
#####  设置头像
	avatar: /images/0.jpg
```
可以指定网址图片url
#### 侧边栏社交链接
侧栏社交链接的修改包含两个部分，第一是链接，第二是链接图标。 两者配置均在 主题配置文件 中。
>链接放置在 social 字段下，一行一个链接。其键值格式是 显示文本: 链接地址

```
# Social links
social:
  GitHub: https://github.com/your-user-name
  Twitter: https://twitter.com/your-user-name
  微博: http://weibo.com/your-user-name
  豆瓣: http://douban.com/people/your-user-name
  知乎: http://www.zhihu.com/people/your-user-name
  # 等等
```
效果图
![lin](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/lin.png)


>设定链接的图标，对应的字段是 social_icons。其键值格式是 匹配键: Font Awesome 图标名称， 匹配键 与上一步所配置的链接的 显示文本 相同（大小写严格匹配），图标名称 是 Font Awesome 图标的名字（不必带 fa- 前缀）。 enable 选项用于控制是否显示图标，你可以设置成 false 来去掉图标。

```
# Social Icons
social_icons:
  enable: true
  # Icon Mappings
  GitHub: github
  Twitter: twitter
  微博: weibo
```
#### 友情链接
>编辑 主题配置文件 添加：

```
# Blogrolls
links_title: 友情链接
links_layout: inline
links_icon: link  # 设置图标
links:
  一俗人: http://yisu.ren/index.php/
```
![yl](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/yl.png)

#### 在右上角或者左上角实现fork me on github

修改F:\blog\themes\next\layout\_layout.swig内容，在</body>上面添加代码，如下

```
<!-- S 新增代码-->
<a href="https://github.com/volc1605"><img style="position: absolute; top: 0; right: 0; border: 0;" src="https://s3.amazonaws.com/github/ribbons/forkme_right_darkblue_121621.png" alt="Fork me on GitHub"></a>
<!-- E 新增代码-->
</body>
```

![a](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/a.png)

### 文章配置
#### 开启阅读全文
>方式一：在文章中进行截断(推荐使用)
以下给出文章md文件片段内容

```
在浏览器访问：http://localhost:4000/ 便可查看hexo默认的主题
![hexo](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/h.png)
<!-- more -->    # 直接在文章的md文件中添加<!-- more -->标注
>clone NexT主题

在blog根目录执行命令
```

>方式二：修改主题配置文件

```
auto_excerpt:
  enable: true  # 开启文章阅全文
  length: 2000   # 显示长度
```
#### 新增阅读量
为每一篇文章统计阅读量  
>使用leancloud进行文章变更统计，进入官网https://leancloud.cn/ 注册

打开LeanCloud官网，进入注册页面注册。完成邮箱激活后，点击头像，进入控制台页面
>创建新应用

点击应用创建新应用，应用名称随便起

>创建Class文件

进入到应用后点击存储，在左侧点击设置，选择"创建Class"，名称必须为Counter
>修改主题配置文件

主题配置文件
```
#### 文章阅读量
leancloud_visitors:
  enable: true
  app_id: **你的app_id**
  app_key: **你的app_key**
```
其中，app_id和app_key在你所创建的应用的设置->应用Key中  
>添加lean-analytics.swig文件

在主题的 themes\next\layout\\_scripts\third-party路径下查看是否有lean-analytics.swig 文件，如果没有新建一个 lean-analytics.swig 文件，并向里面添加以下内容：
```
{% if theme.leancloud_visitors.enable %}

  {# custom analytics part create by xiamo #}
  <script src="https://cdn1.lncld.net/static/js/av-core-mini-0.6.1.js"></script>
  <script>AV.initialize("{{theme.leancloud_visitors.app_id}}", "{{theme.leancloud_visitors.app_key}}");</script>
  <script>
    function showTime(Counter) {
      var query = new AV.Query(Counter);
      var entries = [];
      var $visitors = $(".leancloud_visitors");

      $visitors.each(function () {
        entries.push( $(this).attr("id").trim() );
      });

      query.containedIn('url', entries);
      query.find()
        .done(function (results) {
          var COUNT_CONTAINER_REF = '.leancloud-visitors-count';

          if (results.length === 0) {
            $visitors.find(COUNT_CONTAINER_REF).text(0);
            return;
          }

          for (var i = 0; i < results.length; i++) {
            var item = results[i];
            var url = item.get('url');
            var time = item.get('time');
            var element = document.getElementById(url);

            $(element).find(COUNT_CONTAINER_REF).text(time);
          }
          for(var i = 0; i < entries.length; i++) {
            var url = entries[i];
            var element = document.getElementById(url);
            var countSpan = $(element).find(COUNT_CONTAINER_REF);
            if( countSpan.text() == '') {
              countSpan.text(0);
            }
          }
        })
        .fail(function (object, error) {
          console.log("Error: " + error.code + " " + error.message);
        });
    }

    function addCount(Counter) {
      var $visitors = $(".leancloud_visitors");
      var url = $visitors.attr('id').trim();
      var title = $visitors.attr('data-flag-title').trim();
      var query = new AV.Query(Counter);

      query.equalTo("url", url);
      query.find({
        success: function(results) {
          if (results.length > 0) {
            var counter = results[0];
            counter.fetchWhenSave(true);
            counter.increment("time");
            counter.save(null, {
              success: function(counter) {
                var $element = $(document.getElementById(url));
                $element.find('.leancloud-visitors-count').text(counter.get('time'));
              },
              error: function(counter, error) {
                console.log('Failed to save Visitor num, with error message: ' + error.message);
              }
            });
          } else {
            var newcounter = new Counter();
            /* Set ACL */
            var acl = new AV.ACL();
            acl.setPublicReadAccess(true);
            acl.setPublicWriteAccess(true);
            newcounter.setACL(acl);
            /* End Set ACL */
            newcounter.set("title", title);
            newcounter.set("url", url);
            newcounter.set("time", 1);
            newcounter.save(null, {
              success: function(newcounter) {
                var $element = $(document.getElementById(url));
                $element.find('.leancloud-visitors-count').text(newcounter.get('time'));
              },
              error: function(newcounter, error) {
                console.log('Failed to create');
              }
            });
          }
        },
        error: function(error) {
          console.log('Error:' + error.code + " " + error.message);
        }
      });
    }

    $(function() {
      var Counter = AV.Object.extend("Counter");
      if ($('.leancloud_visitors').length == 1) {
        addCount(Counter);
      } else if ($('.post-title-link').length > 1) {
        showTime(Counter);
      }
    });
  </script>

{% endif %}
```
>修改 themes\next\layout\_macro\post.swig 文件

找到：
```
{% if not is_index and theme.facebook_sdk.enable and theme.facebook_sdk.like_button %}
  &nbsp; | &nbsp;
  <div class="fb-like" data-layout="button_count" data-share="true"></div>
{% endif %}
```
在其后追加以下代码
```
{# LeanCould PageView #}
  {% if theme.leancloud_visitors.enable %}
     <span id="{{ url_for(post.path) }}" class="leancloud_visitors" data-flag-title="{{ post.title }}">
       &nbsp; | &nbsp;
       <span class="post-meta-item-icon">
         <i class="fa fa-eye"></i>
       </span>
       <span class="post-meta-item-text">{{__('post.visitors')}} </span>
       <span class="leancloud-visitors-count"></span>
  {% endif %}
{% endif %}
```

>配置themes\next\layout\\_layout.swig文件

在最后div标签中查找是否引用了_scripts/third-party/lean-analytics.swig文件，如果没有增加以下代码
```
{% include '_scripts/third-party/comments.swig' %}
  {% include '_scripts/third-party/tinysou.swig' %}
  {% include '_scripts/third-party/localsearch.swig' %}
  {% include '_scripts/third-party/mathjax.swig' %}
  {% include '_scripts/third-party/lean-analytics.swig' %}  # 新增
  {% include '_scripts/baidu-push.swig' %}
</body>
```
>修改语言配置文件 themes\next\languages\zh-Hans.yml post字段

```
post:
  sticky: 置顶
  posted: 发表于
  updated: 最近
  update: 更新于
  in: 分类于
  visitors: 阅读量
  read_more: 阅读全文
  untitled: 未命名
  toc_empty: 此文章未包含目录

```
其他语言与之类似，将visitors设置成你希望翻译的字段。最后，重新清除并生成你的网站即可。
>Web安全性

为了保证应用的统计计数功能仅应用于自己的博客系统，你可以在应用->设置->安全中心的Web安全域名中加入自己的博客域名，以保证数据的调用安全。直接加上首页地址即可,保存三分之后生效，这时在本地方访问便不会统计。  
参阅：http://www.jeyzhang.com/hexo-next-add-post-views.html
#### 文章最近访问时间  

>改themes\next\layout\_scripts\third-party\lean-analytics.swig文件

```
{% if theme.leancloud_visitors.enable %}
  {# custom analytics part create by xiamo #}
  <script src="https://cdn1.lncld.net/static/js/av-core-mini-0.6.1.js"></script>
  <script>AV.initialize("{{theme.leancloud_visitors.app_id}}", "{{theme.leancloud_visitors.app_key}}");</script>
  <script>
    function showTime(Counter) {
      var query = new AV.Query(Counter);
      var entries = [];
      var $visitors = $(".leancloud_visitors");
      $visitors.each(function () {
        entries.push( $(this).attr("id").trim() );
      });
      query.containedIn('url', entries);
      query.find()
        .done(function (results) {
          var COUNT_CONTAINER_REF = '.leancloud-visitors-count';
          var UPDATED_AT = '.leancloud-updated-at'; // 新增代码 定义最近变更时间的class
          if (results.length === 0) {
            $visitors.find(COUNT_CONTAINER_REF).text(0);
            return;
          }
          // console.log(results);
          for(var i = 0; i < results.length; i++) {
            var item = results[i];
            var url = item.get('url');
            var time = item.get('time');   // 获取阅读量
            var updatedT = item.updatedAt; // 新增代码 获取最近浏览时间 需要注意的是updateT是对象并不是字符串
            var element = document.getElementById(url);
            $(element).find(COUNT_CONTAINER_REF).text(time);
            // 新增代码 转换日期格式
            if(updatedT != null && updatedT != '') {
              var a = [updatedT.getFullYear(), updatedT.getMonth() + 1, updatedT.getDate(), updatedT.getHours(), updatedT.getMinutes(), updatedT.getSeconds()];
              for(var j = 0,len = a.length; j < len; j++) {
                  if(a[j] < 10) {
                      a[j] = '0' + a[j];
                  }
              }
              var updateTime = a[0] + '-' + a[1] + '-' + a[2] + ' ' + a[3] + ':' + a[4] + ':' + a[5];
              $(element).find(UPDATED_AT).text(updateTime);
            }
          }
          for(var i = 0; i < entries.length; i++) {
            var url = entries[i];
            var element = document.getElementById(url);
            var countSpan = $(element).find(COUNT_CONTAINER_REF);
            var updatedAtContent = $(element).find(UPDATED_AT);
            if( countSpan.text() == '') {
              countSpan.text(0);
            }
            // 新增代码 如果首页的文章没有更新侧默认为无人浏览
            if(updatedAtContent.text() == '') {
              updatedAtContent.text('无人浏览');
            }
            // 结束
          }
        })
        .fail(function (object, error) {
          console.log("Error: " + error.code + " " + error.message);
        });
    }
    function addCount(Counter) {
      var $visitors = $(".leancloud_visitors");
      var url = $visitors.attr('id').trim();
      var title = $visitors.attr('data-flag-title').trim();
      var query = new AV.Query(Counter);
      query.equalTo("url", url);
      query.find({
        success: function(results) {
          if (results.length > 0) {
            var counter = results[0];
            counter.fetchWhenSave(true);
            counter.increment("time");
            counter.save(null, {
              success: function(counter) {
                var $element = $(document.getElementById(url));
                $element.find('.leancloud-visitors-count').text(counter.get('time'));
              },
              error: function(counter, error) {
                console.log('Failed to save Visitor num, with error message: ' + error.message);
              }
            });
          } else {
            var newcounter = new Counter();
            /* Set ACL */
            var acl = new AV.ACL();
            acl.setPublicReadAccess(true);
            acl.setPublicWriteAccess(true);
            newcounter.setACL(acl);
            /* End Set ACL */
            newcounter.set("title", title);
            newcounter.set("url", url);
            newcounter.set("time", 1);
            newcounter.save(null, {
              success: function(newcounter) {
                var $element = $(document.getElementById(url));
                $element.find('.leancloud-visitors-count').text(newcounter.get('time'));
              },
              error: function(newcounter, error) {
                console.log('Failed to create');
              }
            });
          }
        },
        error: function(error) {
          console.log('Error:' + error.code + " " + error.message);
        }
      });
    }
    $(function() {
      var Counter = AV.Object.extend("Counter");
      if ($('.leancloud_visitors').length == 1) {
        addCount(Counter);
      } else if ($('.post-title-link').length > 1) {
        showTime(Counter);
      }
    });
  </script>
{% endif %}
```

#### 添加文章更新时间
>scaffolds\post.md 文件

```
---
title: {{ title }}
date: {{ date }}
categories:
tags:
update: {{ date }}  # 新增更新时间
---
```
>themes\next\layout\_macro\post.swig 文件

找到以下代码的位置

```
<span class="post-time">
  <span class="post-meta-item-icon">
    <i class="fa fa-calendar-o"></i>
  </span>
  <span class="post-meta-item-text">{{ __('post.posted') }}</span>
  <time itemprop="dateCreated" datetime="{{ moment(post.date).format() }}" content="{{ date(post.date, config.date_format) }}">
    {{ date(post.date, config.date_format) }}
  </time>
</span>
```
进行追加
```
<span class="post-time">
  <span class="post-meta-item-icon">
    <i class="fa fa-calendar-o"></i>
  </span>
  <span class="post-meta-item-text">{{ __('post.posted') }}</span>
  <time itemprop="dateCreated" datetime="{{ moment(post.date).format() }}" content="{{ date(post.date, config.date_format) }}">
    {{ date(post.date, config.date_format) }}
  </time>
</span>
{# 追加代码 #}
<span>
    |
  <span>{{ __('post.update') }}</span>
  <time>
    {{ date(post.update, config.date_format) }}
    {{ date(post.update, config.time_format) }}
  </time>
</span>
```
在每次新建文章时，默认更新时间就是发表时间，更新文章时需要手动修改udpate的值。例如source_posts\hexo之next主题.md

```
---
title: Hexo之NexT主题搭建博客详细过程
date: 2016-10-03 15:39:31
update: 2016-10-03 15:39:31      # 每次更新需手动修改成这样的格式时间
categories: hexo
tags: [nodejs, hexo]
---
```
>修改themes\next\languages\zh-Hans.yml

```
post:
  sticky: 置顶
  posted: 发表于
  updated: 最近
  update: 更新于     # 新增
  in: 分类于
  visitors: 阅读量
  read_more: 阅读全文
  untitled: 未命名
  toc_empty: 此文章未包含目录
```

#### 设置文章按照更新时间排序

修改F:\blog\node_modules\hexo-generator-index\lib\generator.js文件，内容如下
```
'use strict';
var pagination = require('hexo-pagination');
module.exports = function(locals){
  var config = this.config;
  var posts = locals.posts;
  posts.data = posts.data.sort(function(a, b) {
	return Date.parse(b.update) - Date.parse(a.update); // 按更新时间降序
  });
  var paginationDir = config.pagination_dir || 'page';
  return pagination('', posts, {
    perPage: config.index_generator.per_page,
    layout: ['index', 'archive'],
    format: paginationDir + '/%d/',
    data: {
      __index: true
    }
  });
};

```

#### 设置阅读全文时不显示最近访问时间
>修改themes\next\layout\_macro\post.swig 文件

```
{# LeanCould PageView #}
  {% if theme.leancloud_visitors.enable %}
     <span id="{{ url_for(post.path) }}" class="leancloud_visitors" data-flag-title="{{ post.title }}">
       |
       <span class="post-meta-item-icon">
         <i class="fa fa-eye"></i>
       </span>
       <span class="post-meta-item-text">{{__('post.visitors')}} </span>
       <span class="leancloud-visitors-count"></span>
      {% if not is_pv %}    {# 新增is_pv字段判断是否为首页 #}
        |
        <span class="post-meta-item-text">{{__('post.updated')}} </span>
        <span class="leancloud-updated-at"></span>
        </span>
      {% endif %}           {# if结束 #}
{% endif %}
```
在文件开头修改传进的参数字段
```
{% macro render(post, is_index, is_pv, post_extra_class) %}   {# 新增第三个字段is_pv #}
```
>修改themes/next/layout目录下的post.swig文件

这个文件是文章的模板
```
{% block content %}
  <div id="posts" class="posts-expand">
  	#render方法第二个参数is_index为是否为首页
  	#第三个参数is_pv为我们刚才设置的是否显示计数
    {{ post_template.render(page, false, true) }}
    <div class="post-spread">
      {% if theme.jiathis %}
        {% include '_partials/share/jiathis.swig' %}
      {% elseif theme.duoshuo_shortname and theme.duoshuo_share %}
        {% include '_partials/share/duoshuo_share.swig' %}
      {% endif %}
    </div>
  </div>
{% endblock %}
```
>修改themes/next/layout目录下的index.swig文件

这个文件是首页的模板
```
找到以下代码，给render方法传入刚才第一步中设置的参数。
{% block content %}
  <section id="posts" class="posts-expand">
    {% for post in page.posts %}
       #render方法第二个参数is_index为是否为首页
       #第三个参数is_pv为我们刚才设置的是否显示计数
      {{ post_template.render(post, true, false) }}
    {% endfor %}
  </section>
  {% include '_partials/pagination.swig' %}
{% endblock %}
```

这样的效果是在首页显示最近阅读时间，阅读全文时文章标题不显示最近阅读时间。


#### 为文章增加百度分享

因为百度分享插件不支持https，所以需要手动引用百度分享。
##### 下载修复过的百度分享插件
下载地址： https://github.com/hrwhisper/baiduShare，解压到\themes\next\source目录下

##### 开启百度分享
在\themes\next\_config.yml添加如下配置

```
baidushare:
  type: button
  baidushare: true
```

##### 引用百度分享
\themes\next\layout\post.swig

```
<div id="posts" class="posts-expand">
    {{ post_template.render(page, false, true) }}
    <div class="post-spread">
      {% if theme.jiathis %}
        {% include '_partials/share/jiathis.swig' %}
      {% elseif theme.baidushare %}
        {% include '_partials/share/baidushare.swig' %}
      {% elseif theme.add_this_id %}
        {% include '_partials/share/add-this.swig' %}
      {% endif %}
    </div>
  </div>
```
##### 配置分享按钮

\themes\next\layout\_partials\share\baidushare.swig

```
{% if theme.baidushare.type === "button" %}
  <div class="bdsharebuttonbox">
    <a href="#" class="bds_tsina" data-cmd="tsina" title="分享到新浪微博"></a>
    <a href="#" class="bds_douban" data-cmd="douban" title="分享到豆瓣网"></a>
    <a href="#" class="bds_sqq" data-cmd="sqq" title="分享到QQ好友"></a>
    <a href="#" class="bds_qzone" data-cmd="qzone" title="分享到QQ空间"></a>
    <a href="#" class="bds_weixin" data-cmd="weixin" title="分享到微信"></a>
    <a href="#" class="bds_tieba" data-cmd="tieba" title="分享到百度贴吧"></a>
    <a href="#" class="bds_twi" data-cmd="twi" title="分享到Twitter"></a>
    <a href="#" class="bds_fbook" data-cmd="fbook" title="分享到Facebook"></a>
    <a href="#" class="bds_more" data-cmd="more"></a>
    <a class="bds_count" data-cmd="count"></a>
  </div>
  <script>
    window._bd_share_config = {
      "common": {
        "bdText": "",
        "bdMini": "2",
        "bdMiniList": false,
        "bdPic": ""
      },
      "share": {
        "bdSize": "16",
        "bdStyle": "0"
      },
      "image": {
        "viewList": ["tsina", "douban", "sqq", "qzone", "weixin", "twi", "fbook"],
        "viewText": "分享到：",
        "viewSize": "16"
      }
    }
  </script>
{% elseif theme.baidushare.type === "slide" %}
  <script>
    window._bd_share_config = {
      "common": {
        "bdText": "",
        "bdMini": "1",
        "bdMiniList": false,
        "bdPic": ""
      },
      "image": {
        "viewList": ["tsina", "douban", "sqq", "qzone", "weixin", "twi", "fbook"],
        "viewText": "分享到：",
        "viewSize": "16"
      },
      "slide": {
        "bdImg": "5",
        "bdPos": "left",
        "bdTop": "100"
      }
    }
  </script>
{% endif %}
<script>
<!-- S 修改代码，引用本地百度分享插件 -->
  with(document)0[(getElementsByTagName('head')[0]||body).appendChild(createElement('script')).src='/static/api/js/share.js?v=89860593.js?'];
<!-- E 修改代码 -->
</script>
```

![i](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/i.png)

#### 为文章增加打赏
>修改主题配置文件, 收款二维码放在themes\next\source\images 目录下

```
# Donate 文章末尾显示打赏按钮
reward_comment: 如果文章对您有用请随意打赏，谢谢支持！
wechatpay: /images/w.png
alipay: /images/z.jpg
```

#### 设置代码高亮主题
NexT 使用 Tomorrow Theme 作为代码高亮，共有5款主题供你选择。 NexT 默认使用的是 白色的 normal 主题，可选的值有 normal，night， night blue， night bright， night eighties：
在主题配置文件中修改
```
# Code Highlight theme
# Available value:
#    normal | night | night eighties | night blue | night bright
# https://github.com/chriskempson/tomorrow-theme
highlight_theme: night eighties       # 修改即可
```
#### 文章代码块字体

修改\themes\next\source\css\_variables\base.styl

```
// Code & Code Blocks
// --------------------------------------------------
$code-font-family               = consolas
$code-font-size                 = 14px
$code-background                = $gainsboro
$code-foreground                = $black-light
$code-border-radius             = 4px
```

#### 文章添加音乐链接
Hexo支持解析markdown语法，因此每篇博文都是以.md结尾的文件。而markdown又支持如表格、脚注、内嵌HTML等等，所以在.md文件中直接添加html代码！  
网音乐云音乐，虾米音乐都可以生成内嵌音乐的html代码，复制粘贴到.md文件中即可  
```
<div>
	<center>
		<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86
			src="https://music.163.com/outchain/player?type=2&id=33856282&auto=0&height=66">
		</iframe>
	</center>
</div>
```
#### 修改文章底部的那个带#号的标签
修改F:\blog\themes\next\layout\_macro\post.swig文件，搜索 rel="tag">#，将 # 换成<i class="fa fa-tag"></i>

```
<a href="{{ url_for(tag.path) }}" rel="tag"><i class="fa fa-tag"></i>{{ tag.name }}</a>
```

![b](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/b.png)

#### 主页文章添加阴影效果

修改F:\blog\themes\next\source\css\_custom\custom.styl文件，内容如下

```
// Custom styles.
// 主页文章添加阴影效果
 .post {
   margin-top: 60px;
   margin-bottom: 60px;
   padding: 25px;
   -webkit-box-shadow: 0 0 5px rgba(202, 203, 203, .5);
   -moz-box-shadow: 0 0 5px rgba(202, 203, 204, .5);
  }
```

![c](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/c.png)

#### 为文章添加结束标记

\themes\next\_config.yml中新增配置如下

```
passage_end_tag: 
    enabled: true
```

创建文件\themes\next\layout\_macro\passage-end-tag.swig，内容如下

```
<div>
    {% if not is_index %}
        <div style="text-align:center;color: #807272;font-size:14px;">-------------本文结束<i class="fa fa-paw"></i>感谢您的阅读-------------</div>
    {% endif %}
</div>
```

修改\themes\next\layout\_macro\post.swig

```
	<!-- S 新增代码-->
	<div>
		{% if not is_index %}
			{% include 'passage-end-tag.swig' %}
		{% endif %}
	</div>
	<!-- E 新增代码-->
    <div>
      {% if not is_index %}
        {% include 'wechat-subscriber.swig' %}
      {% endif %}
    </div>

    <div>
      {% if not is_index %}
        {% include 'reward.swig' %}
      {% endif %}
    </div>

    <footer class="post-footer">
```

![f](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/f.png)

### 本地站内搜索
以前使用的Swiftype现在不能免费使用了，我这里就是用本地配置进行站内搜索
>安装 hexo-generator-search，在站点的根目录下执行以下命令:

```apache
npm install hexo-generator-search --save
```
>编辑 站点配置文件，新增以下内容到任意位置：

```
search:
  path: search.xml
  field: post
```

修改主题配置文件

```
local_search:
  enable: true
```

### 统计站点访次数和访问量
>修改themes/next/layout/_partials目录下的footer.swig

```
<div class="theme-info">
  {{ __('footer.theme') }} -
  <a class="theme-link" href="https://github.com/iissnan/hexo-theme-next">
    NexT.{{ theme.scheme }}
  </a>
</div>
{# 新增代码 #}
<script async src="https://dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js"></script>
<div style="margin:-20px;position: absolute;right:265px;">
<dl style="float:left">
  <dt style="float:left">
    <dd style="float:left;width:300px">
      <span style="float:right">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|</span>
      <span style="float:right">本站总访问量<span><span style="float:right" id="busuanzi_value_site_pv"></span>
    </dd>
  </dt>
  <dt style="float:left;width:300px">
    <dd>
      <span style="float:left">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span>
      <span style="float:left">您是第</span>
      <span style="float:left" id="busuanzi_value_site_uv"></span>
      <span style="float:left">个小伙伴</span>
    </dd>
  </dt>
</dl>
</div>
{# 结束 #}
```
到此本地资源博客搭建已经完成，有些细微调节可根据浏览器控制台找到对应的模板进行调节，比如站点背景(可以指定动画图片，参考资源： http://michaelxiang.me/2015/11/30/hexo-next-optimize/ )，代码字体字体颜色(参考资源： http://gniba.com/2016/07/11/next-custom.html )，页面宽度等等
### 博客项目托管到github
>注册github账号

网址：https://github.com/
点击 new repository
![xinjian](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/g.png)
>修改站点配置文件

```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repository: git@github.com:volc1605/volc1612.gitee.io/blog.git
  branch: master
```
点击进入新建的项目，如图
![xj](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/d.png)
```
repository： git@github.com:上图划线部分.git
```
>配置sshkey

```apache
ssh-keygen -t rsa -C "注册git的邮箱"
```
连续几个按回车，密码为空，然后在在C:\Users\Administrator.ssh下，得到两个文件id_rsa和id_rsa.pub
复制id_rsa.pub中的所有内容
>GitHub上添加SSH密钥

打开https://github.com/settings/ssh new SSH key 添加密钥，title随便写，key为刚刚复制的内容

>验证是否能否连接到github

```apache
$ ssh -T git@github.com
```
如果提示
```apache
The authenticity of host 'github.com (192.30.253.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? y
Please type 'yes' or 'no': yes
```
或者提示覆盖原来的文件，选择yes回车，只要能看到，如下提示就能成功连接到github

```apache
Hi zhangsan! You've successfully authenticated, but GitHub does not provide shell access.
```
### 百度/google收录你的站点
>安装sitemap插件

```apache
npm install hexo-generator-sitemap --save        # google
npm install hexo-generator-baidu-sitemap --save  # 百度
```
>在站点配置文件_config.yml中添加如下代码：

```
# 自动生成sitemap
sitemap:
path: sitemap.xml
baidusitemap:
path: baidusitemap.xml
```
会在public目录下生成baidusitmap.xml 和 sitemap.xml两个文件  
>安装hexo-deployer-git插件

```apache
npm install hexo-deployer-git --save
```
>部署到github

在blog目录下执行命令
```apache
hexo clean # 清除资源
hexo g     # 生成静态资源
hexo d     # 部署
```
登录在github打开存放博客资源的仓库，就能看到部署的资源文件
![](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/git.png)

>分别向google,baidu提交站点地图sitemap.xml,baidusitmap.xml

百度站长： http://zhanzhang.baidu.com/site/index
输入你想添加的网站：你的博客首页地址
下一步，下载html文件验证，在原来内容上添加
```
layout: false
---
```
例如
```
layout: false
---
HTYJLXSGJOnjlg     
```
目的是不让站点解析这个baidu_verify_lpmmZiuzEL.html文件，修改完成后放到blog/source目录下重新部署，然后点击完成验证。  
google站长： https://www.google.com/webmasters/tools/home?hl=zh-CN
添加网站后，进行左侧抓取站点地图，添加/测试站点地图，填写sitemap.xml。sitemap.xml就在github存放博客仓库的根目录下
验证完成后大概过一天时间便可google到你的站点了。
而百度的话，就看造化了。

### 关于github访问不到css、js文件  

浏览器控制可以看到问题所在访问不到vendors/source
![vendors](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/vendors.png)
问题原因不清楚，但通过一下方式可以解决问题  
根据作者的提示 @iissnan ，首先修改source/vendors为source/lib，然后修改_config.yml， 将 _internal: vendors修改为_internal:lib 然后修改next底下所有引用source/vendors路径为source/lib。这些地方可以通过文件查找找出来。  
主要集中在这几个文件中。  
1. Hexo\themes\next.bowerrc   
2. Hexo\themes\next.gitignore   
3. Hexo\themes\next.javascript_ignore   
4. Hexo\themes\next\bower.json   


### 统计本站运行天数

修改blog\themes\next\layout\_partials\footer.swig文件，在后面追加以下代码
```javascript
<script>
	var birthDay = new Date('09/1/2016');
	var now = new Date();
	var duration = now.getTime() - birthDay.getTime();
	var total= Math.floor(duration / (1000 * 60 * 60 * 24));
	document.getElementById('showDays').innerHTML='本站已运行' + total + '天';
</script>
```
在最后一个div中追加一下代码
```javascript
<span>&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;</span>
<span id="showDays"></span>
```

### 阿里云解析域名到github
![](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/jiexi.png)
在博客根目录添加CNAME文件
![](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/cname.png)

### 引入gitment评论
由于阿里云清除了空壳网站的备案信息，畅言有可能用不了了！！下面使用git自带的评论。

```
// http://blog.csdn.net/yanzi1225627/article/details/77890414(hexo集成gitment评论)
```

### 打开博客时显示进度条
修改F:\blog\themes\next\layout\_partials\head.swig
在<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1"/>下面增加如下代码
```
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1"/>
<!-- S 新增代码 -->
<script src="//cdn.bootcss.com/pace/1.0.2/pace.min.js"></script>
<link href="//cdn.bootcss.com/pace/1.0.2/themes/pink/pace-theme-flash.css" rel="stylesheet">
<style>
    .pace .pace-progress {
        background: #24292e; /*进度条颜色*/
        height: 3px;
    }
    .pace .pace-progress-inner {
         box-shadow: 0 0 10px #1E92FB, 0 0 5px     #1E92FB; /*阴影颜色*/
    }
    .pace .pace-activity {
        border-top-color: #1E92FB;    /*上边框颜色*/
        border-left-color: #1E92FB;    /*左边框颜色*/
    }
</style>
<!-- E 新增代码 -->
```

![d](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/d.gif)

### 样式配置文件

\themes\next\source\css\_custom\custom.stylcustom.style
```
// Custom styles
//首页头部样式
.header {
    background: url("/images/header-bk.jpg");
}
.site-meta {
    margin-left: 0px;
    text-align: center;
}
.site-meta .site-title {
    font-size: 20px;
    font-family: 'Comic Sans MS', sans-serif;
    color: #fff;
	letter-spacing: 1px;
	width: 81%;
}
// 页面背景色
.container {
    background-color: rgba(0, 0, 0, 0.75);
}
// 页面留白更改
.header-inner {
    padding-top: 0px;
    padding-bottom: 0px;
}
.posts-expand {
    padding-top: 80px;
}
.posts-expand .post-meta {
    margin: 5px 0px 0px 0px;
}
.post-button {
    margin-top: 0px;
}
// 顶栏宽度
.container .header-inner {
    width: 100%;
}
// 站点名背景
.brand{
    background-color: rgba(255, 255, 255, 0);
    margin-top: 15px;
    padding: 0px;
}
// 站点名字体
.site-title {
    line-height: 35px;
    letter-spacing: 3px;
}
// 站点子标题
.site-subtitle{ 
    margin: 0px;
    font-size: 16px;
    letter-spacing: 1px;
    padding-bottom: 3px;
    font-weight: bold;
    color: rgb(255, 255, 255);
    border-bottom-width: 3px;
    border-bottom-style: solid;
    border-bottom-color: rgb(161, 102, 171);
}
.logo-line-after {
    display: none;
}
.logo-line-before {
    display: none;
}
// 菜单
.menu {
    float: none;
}

// 菜单超链接字体大小
.menu .menu-item a {
    font-size: 14px;
    color: rgb(15, 46, 65);
    border-radius: 4px;
}
// 菜单各项边距
.menu .menu-item {
    margin: 5px 15px;
}
// 菜单超链接样式
.menu .menu-item a:hover {
    border-bottom-color: rgba(161, 102, 171, 0);
}
// 文章
.post {
    margin-bottom: 50px;
    padding: 45px 36px 36px 36px;
    box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.5);
    background-color: rgb(255, 255, 255);
}
// 文章标题字体
.posts-expand .post-title {
    font-size: 26px;
    font-weight: 700;
}
// 文章标题动态效果
.posts-expand .post-title-link::before {
    background-image: linear-gradient(90deg, #a166ab 0%, #ef4e7b 25%, #f37055 50%, #ef4e7b 75%, #a166ab 100%);
}
// 文章元数据（meta）留白更改
.posts-expand .post-meta {
    margin: 10px 0px 20px 0px;
}
// 文章的描述description
.posts-expand .post-meta .post-description {
    font-style: italic;
    font-size: 14px;
    margin-top: 30px;
    margin-bottom: 0px;
    color: #666;
}
// [Read More]按钮样式
.post-button .btn {
    color: #555 !important;
    background-color: rgb(255, 255, 255);
    border-radius: 3px;
    font-size: 15px;
    box-shadow: inset 0px 0px 10px 0px rgba(0, 0, 0, 0.35);
    border: none !important;
    transition-property: unset;
    padding: 0px 15px;
}
.post-button .btn:hover {
    color: rgb(255, 255, 255) !important;
    border-radius: 3px;
    font-size: 15px;
    box-shadow: inset 0px 0px 10px 0px rgba(0, 0, 0, 0.35);
    background-image: linear-gradient(90deg, #a166ab 0%, #ef4e7b 25%, #f37055 50%, #ef4e7b 75%, #a166ab 100%);
}
// 去除在页面文章之间的分割线
.posts-expand .post-eof {
    margin: 0px;
    background-color: rgba(255, 255, 255, 0);
}
// 去除页面底部页码上面的横线
.pagination {
    border: none;
    margin: 0px;
}
// 页面底部页码
.pagination .page-number.current {
    border-radius: 100%;
    box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.5);
    background-color: rgba(255, 255, 255, 0.35);
}
.pagination .prev, .pagination .next, .pagination .page-number {
    margin-bottom: 10px;
    border: none;
}
.pagination .space {
    color: rgb(255, 255, 255);
}
// 页面底部页脚
.footer {
    line-height: 1.5;
    background-color: rgba(255, 255, 255, 0.75);
    color: #333;
    border-top-width: 3px;
    border-top-style: solid;
    border-top-color: rgb(161, 102, 171);
    box-shadow: 0px -10px 10px 0px rgba(0, 0, 0, 0.15);
}
// 文章底部的tags
.posts-expand .post-tags a {
    border-bottom: none;
    margin-right: 0px;
    font-size: 13px;
    padding: 0px 5px;
    border-radius: 3px;
    transition-duration: 0.2s;
    transition-timing-function: ease-in-out;
    transition-delay: 0s;
}
.posts-expand .post-tags a:hover {
    background: #eee;
}
// 文章底部留白更改
.post-widgets {
    padding-top: 0px;
}
.post-nav {
    margin-top: 30px;
}
// 文章底部页面跳转
.post-nav-item a {
    color: rgb(80, 115, 184);
    font-weight: bold;
}
.post-nav-item a:hover {
    color: rgb(161, 102, 171);
    font-weight: bold;
}
// 文章底部评论
.comments {
    background-color: rgb(255, 255, 255);
    box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.35);
    margin: 80px 0px 40px 0px;
}
// 超链接样式
a {
    color: rgb(80, 115, 184);
    border-bottom-color: rgb(80, 115, 184);
}
a:hover {
    color: rgb(161, 102, 171);
    border-bottom-color: rgb(161, 102, 171);
}
// 分割线样式
hr {
    margin: 10px 0px 30px 0px;
}
// 文章内标题样式（左边的竖线）
.post-body h2, h3, h4, h5, h6 {
    border-left: 4px solid rgb(161, 102, 171);
    margin-left: -36px;
    padding-left: 32px;
}
// 去掉图片边框
.posts-expand .post-body img {
    border: none;
    padding: 0px;
}
.post-gallery .post-gallery-img img {
    padding: 3px;
}
// 文章``代码块的自定义样式
code {
    margin: 0px 4px;
}
// 文章```代码块顶部样式
.highlight figcaption {
    margin: 0em;
    padding: 0.5em;
    background: #eee;
    border-bottom: 1px solid #e9e9e9;
}
.highlight figcaption a {
    color: rgb(80, 115, 184);
}
// 文章```代码块diff样式
pre .addition {
    background: #e6ffed;
}
pre .deletion {
    background: #ffeef0;
}
// 右下角侧栏按钮样式
.sidebar-toggle {
    right: 10px;
    bottom: 43px;
    background-color: rgba(247, 149, 51, 0.75);
    border-radius: 5px;
    box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.35);
}
.page-post-detail .sidebar-toggle-line {
    background: rgb(7, 179, 155);
}
// 右下角返回顶部按钮样式
.back-to-top {
    line-height: 1.5;
    right: 10px;
    padding-right: 5px;
    padding-left: 5px;
    padding-top: 2.5px;
    padding-bottom: 2.5px;
    background-color: rgba(247, 149, 51, 0.75);
    border-radius: 5px;
    box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.35);
}
.back-to-top.back-to-top-on {
    bottom: 10px;
}
// 侧栏
.sidebar {
    box-shadow: inset 0px 0px 10px 0px rgba(0, 0, 0, 0.5);
    background-color: rgba(0, 0, 0, 0.75);
}
.sidebar-inner {
    margin-top: 30px;
}
// 侧栏顶部文字
.sidebar-nav li {
    font-size: 15px;
    font-weight: bold;
    color: rgb(7, 179, 155);
}
.sidebar-nav li:hover {
    color: rgb(161, 102, 171);
}
.sidebar-nav .sidebar-nav-active {
    color: rgb(7, 179, 155);
    border-bottom-color: rgb(161, 102, 171);
    border-bottom-width: 1.5px;
}
.sidebar-nav .sidebar-nav-active:hover {
    color: rgb(7, 179, 155);
}
// 侧栏站点概况行高
.site-overview {
    line-height: 1.3;
}
// 侧栏头像（圆形以及旋转效果）
.site-author-image {
    border: 2px solid rgb(255, 255, 255);
    border-radius: 100%;
    transition: transform 1.0s ease-out;
}
img:hover {
    transform: rotateZ(360deg);
}
.posts-expand .post-body img:hover {
    transform: initial;
}
// 侧栏站点作者名
.site-author-name {
    display: none;
}
// 侧栏站点描述
.site-description {
    letter-spacing: 5px;
    font-size: 15px;
    font-weight: bold;
    margin-top: 15px;
    margin-left: 13px;
    color: rgb(243, 112, 85);
}
// 侧栏站点文章、分类、标签
.site-state {
    line-height: 1.3;
    margin-left: 12px;
}
.site-state-item {
    padding: 0px 15px;
    border-left: 1.5px solid rgb(161, 102, 171);
}
// 侧栏RSS按钮样式
.feed-link {
    margin-top: 15px;
    margin-left: 7px;
}
.feed-link a {
    color: rgb(255, 255, 255);
    border: 1px solid rgb(158, 158, 158) !important;
    border-radius: 15px;
}
.feed-link a:hover {
    background-color: rgb(161, 102, 171);
}
.feed-link a i {
    color: rgb(255, 255, 255);
}
// 侧栏社交链接
.links-of-author {
    margin-top: 0px;
}
// 侧栏友链标题
.links-of-blogroll-title {
    margin-bottom: 10px;
    margin-top: 15px;
    color: rgba(7, 179, 155, 0.75);
    margin-left: 6px;
    font-size: 15px;
    font-weight: bold;
}
// 侧栏超链接样式（友链的样式）
.sidebar a {
    color: #ccc;
    border-bottom: none;
}
.sidebar a:hover {
    color: rgb(255, 255, 255);
}
// 自定义的侧栏时间样式
#days {
    display: block;
    color: rgb(7, 179, 155);
    font-size: 13px;
    margin-top: 15px;
}
// 侧栏目录链接样式
.post-toc ol a {
    color: rgb(7, 179, 155);
    border-bottom: 1px solid rgb(96, 125, 139);
}
.post-toc ol a:hover {
    color: rgb(161, 102, 171);
    border-bottom-color: rgb(161, 102, 171);
}
// 侧栏目录链接样式之当前目录
.post-toc .nav .active > a {
    color: rgb(161, 102, 171);
    border-bottom-color: rgb(161, 102, 171);
}
.post-toc .nav .active > a:hover {
    color: rgb(161, 102, 171);
    border-bottom-color: rgb(161, 102, 171);
}
/* 修侧栏目录bug，如果主题配置文件_config.yml的toc是wrap: true */
.post-toc ol {
    padding: 0px 10px 5px 10px;
}
/* 侧栏目录默认全展开，已注释
.post-toc .nav .nav-child {
    display: block;
}
*/
// 时间轴样式
.posts-collapse {
    margin: 50px 0px;
}
@media (max-width: 1023px) {
    .posts-collapse {
        margin: 50px 20px;
    }
}
// 时间轴左边线条
.posts-collapse::after {
    margin-left: -2px;
    background-image: linear-gradient(180deg,#f79533 0,#f37055 15%,#ef4e7b 30%,#a166ab 44%,#5073b8 58%,#1098ad 72%,#07b39b 86%,#6dba82 100%);
}
// 时间轴左边线条圆点颜色
.posts-collapse .collection-title::before {
    background-color: rgb(255, 255, 255);
}
// 时间轴文章标题左边圆点颜色
.posts-collapse .post-header:hover::before {
    background-color: rgb(161, 102, 171);
}
// 时间轴年份
.posts-collapse .collection-title h1, .posts-collapse .collection-title h2 {
    color: rgb(255, 255, 255);
}
// 时间轴文章标题
.posts-collapse .post-title a {
    color: rgb(80, 115, 184);
}
.posts-collapse .post-title a:hover {
    color: rgb(161, 102, 171);
}
// 时间轴文章标题底部虚线
.posts-collapse .post-header:hover {
    border-bottom-color: rgb(161, 102, 171);
}
// archives页面顶部文字
.page-archive .archive-page-counter {
    color: rgb(255, 255, 255);
}
// archives页面时间轴左边线条第一个圆点颜色
.page-archive .posts-collapse .archive-move-on {
    top: 10px;
    opacity: 1;
    background-color: rgb(255, 255, 255);
    box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.5);
}
// 分类页面
.post-block.page {
    margin-top: 40px;
}
.category-all-page {
    margin: -80px 50px 40px 50px;
    box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.5);
    background-color: rgb(255, 255, 255);
    padding: 86px 36px 36px 36px;
}
@media (max-width: 767px) {
    .category-all-page {
        margin: -73px 15px 50px 15px;
    }
    .category-all-page .category-all-title {
        margin-top: -5px;
    }
}
// 标签云页面
.tag-cloud {
    margin: -80px 50px 40px 50px;
    box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.5);
    background-color: rgb(255, 255, 255);
    padding: 86px 36px 36px 36px;
}
.tag-cloud-title {
    margin-bottom: 15px;
}
@media (max-width: 767px) {
    .tag-cloud {
        margin: -73px 15px 50px 15px;
        padding: 86px 5px 36px 5px;
    }
}
// 自定义的TopX页面样式
#top {
    display: block;
    text-align: center;
    margin: -100px 50px 40px 50px;
    box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.5);
    background-color: rgb(255, 255, 255);
    padding: 106px 36px 10px 36px;
}
@media (max-width: 767px) {
    #top {
        margin: -93px 15px 50px 15px;
        padding: 96px 10px 0px 10px;
    }
}
// 自定义ABOUT页面的样式
.about-page {
    margin: -80px 0px 60px 0px;
    box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.5);
    background-color: rgb(255, 255, 255);
    padding: 106px 36px 36px 36px;
}
@media (max-width: 767px) {
    .about-page {
        margin: -73px 0px 50px 0px;
        padding: 96px 15px 20px 15px;
    }
}
h2.about-title {
    border-left: none !important;
    margin-left: 0px !important;
    padding-left: 0px !important;
    text-align: center;
    background-image: linear-gradient(90deg, #a166ab 0%, #a166ab 40%, #ef4e7b 45%, #f37055 50%, #ef4e7b 55%, #a166ab 60%, #a166ab 100%);
    background-size: cover;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    user-select: none;
}
// 本地搜索框
.local-search-popup .search-icon, .local-search-popup .popup-btn-close {
    color: rgb(247, 149, 51);
    margin-top: 7px;
}
.local-search-popup .local-search-input-wrapper input {
    padding: 9px 0px;
    height: 21px;
    background-color: rgb(255, 255, 255);
}
.local-search-popup .popup-btn-close {
    border-left: none;
}
// 选中文字部分的样式
::selection {
    background-color: rgb(255, 241, 89);
    color: #555;
}
/* 设置滚动条的样式 */
/* 参考https://segmentfault.com/a/1190000003708894 */
::-webkit-scrollbar {
    height: 5px;
}
/* 滚动槽 */
::-webkit-scrollbar-track {
    background: #eee;
}
/* 滚动条滑块 */
::-webkit-scrollbar-thumb {
    border-radius: 5px;
    background-color: #ccc;
}
::-webkit-scrollbar-thumb:hover {
    background-color: rgb(247, 149, 51);
}
// 音乐播放器aplayer
.aplayer {
    font-family: Lato, -apple-system, BlinkMacSystemFont, "PingFang SC", "Hiragino Sans GB", "Heiti SC", STHeiti, "Source Han Sans SC", "Noto Sans CJK SC", "WenQuanYi Micro Hei", "Droid Sans Fallback", "Microsoft YaHei", sans-serif !important;
}
.aplayer-withlrc.aplayer .aplayer-info {
    background-color: rgb(255, 255, 255);
}
// 音乐播放器aplayer歌单
.aplayer .aplayer-list ol {
    background-color: rgb(255, 255, 255);
}
// 修视频播放器dplayer页面全屏的bug
.use-motion .post-body {
    transform: inherit !important;
}
// 自定义emoji样式
img#github-emoji {
    margin: 0px;
    padding: 0px;
    display: inline !important;
    vertical-align: text-bottom;
    border: none;
    cursor: text;
    box-shadow: none;
}

.site-meta .brand {   
    width: 82%;
}

// 页面最顶部的横线
.headband {
    height: 1.5px;
    background-image: linear-gradient(90deg, #F79533 0%, #F37055 15%, #EF4E7B 30%, #A166AB 44%, #5073B8 58%, #1098AD 72%, #07B39B 86%, #6DBA82 100%);
}
```

### 使用来必力评论系统

注册来必力和安装

![e](https://volc1612.gitee.io/blog/images/Hexo之NexT主题搭建博客详细过程/e.png)

修改主题配置文件\themes\next\_config.yml，在最后添加
```
livere_uid: 上图data-uid
```

修改\themes\next\layout\_partials\comments.swig，内容如下

```
{% if page.comments %}
  <div class="comments" id="comments">
    {% if (theme.duoshuo and theme.duoshuo.shortname) or theme.duoshuo_shortname %}
      <div class="ds-thread" data-thread-key="{{ page.path }}"
           data-title="{{ page.title }}" data-url="{{ page.permalink }}">
      </div>
	{% elseif theme.livere_uid %}
      <div id="lv-container" data-id="city" data-uid="{{ theme.livere_uid }}"></div>
    {% endif %}
  </div>
{% endif %}

```

修改\themes\next\layout\_scripts\third-party\comments.swig，内容如下

```
{% include './comments/livere.swig' %}
```

重启

```
$ hexo clean
$ hexo s
```
### 设置页面底部心跳

~/blog/themes/next/_config.yml
```
footer:
-  icon: user
+  icon: heart
```

~/blog/themes/next/layout/_partials/footer.swig

```
- <span class="with-love">
+ <span class="with-love" id="heart">
```
~/blog/themes/next/source/css/_custom/custom.styl

```
// 自定义页脚跳动的心样式
@keyframes heartAnimate {
    0%,100%{transform:scale(1);}
    10%,30%{transform:scale(0.9);}
    20%,40%,60%,80%{transform:scale(1.1);}
    50%,70%{transform:scale(1.1);}
}
#heart {
    animation: heartAnimate 1.33s ease-in-out infinite;
}
.with-love {
    color: rgb(255, 113, 168);
}
```

### 自定义背景图片

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery-backstretch/2.0.4/jquery.backstretch.min.js"></script>;
<script>
$("body").backstretch("https://背景图.jpg");
</script>
```

 ~/blog/themes/next/layout/_layout.swig 的最后面</body>前面即可

>参考资料：   
  为NexT主题添加文章阅读量统计功能:        
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;http://www.tuicool.com/articles/YB3EJnz  
git - 简易指南:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;http://www.bootcss.com/p/git-guide/   
hexo之next主题优化整理:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;http://michaelxiang.me/2015/11/30/hexo-next-optimize/  
自定义NexT.Pisces主题:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;http://gniba.com/2016/07/11/next-custom.html  
为Hexo博客的每一篇文章自动追加版权信息:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;http://kuangqi.me/tricks/append-a-copyright-info-after-every-post/  
为Hexo博客添加版权说明和打赏功能:   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;http://www.tuicool.com/articles/m6bIJfq  
[CSS] 给 HEXO 博客自定义背景图:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;http://blog.qiji.tech/archives/7854  
主题配置:   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;http://theme-next.iissnan.com/theme-settings.html  
多说、RSS和sitemap插件:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;http://blog.csdn.net/willxue123/article/details/50994854  
在Github上面搭建Hexo博客（一）：部署到Github:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;http://www.cnblogs.com/cherishzy/p/5694658.html  
第三方服务集成:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;http://theme-next.iissnan.com/third-party-services.html#analytics-google
优化next：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;https://blog.csdn.net/qq_33699981/article/details/72716951
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;https://reuixiy.github.io/technology/computer/computer-aided-art/2017/06/09/hexo-next-optimization.html