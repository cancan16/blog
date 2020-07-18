---
title: 设置CentOS虚拟机静态ip
date: 2020-07-18 21:11:32
update: 2020-07-18 21:11:32
categories: VMware
tags: [VMware]
---


<div style="text-align: center"><iframe height="60" width="260" src="https://www.ximalaya.com/thirdparty/player/sound/player.html?id=264671830&type=red" frameborder=0 allowfullscreen></iframe></div>
                            
<img style="width: 80%;height:80%" src="https://volc1612.gitee.io/blog/images/设置CentOS虚拟机静态ip/黄山.jpg"/>


* 查看宿主机ip

打开`VMware Fusion`,启动你的虚拟机，并在“设置–网络适配器中”选择“桥接模式网络连接”

![宿主机ip]](https://volc1612.gitee.io/blog/images/设置CentOS虚拟机静态ip/查看宿主机ip.png)

* 手动给虚拟机`linux`配置一个`IP`，这个`IP`必须要与主机在同一个网段(192.168.1.1-192.168.1.5)中，否则无法桥接通连，配置方法如下：

```sh
[root@centos7desk ~]# cd /etc/sysconfig/network-scripts
[root@centos7desk network-scripts]# ls
ifcfg-ens33  ifdown-isdn      ifup          ifup-plip      ifup-tunnel
ifcfg-lo     ifdown-post      ifup-aliases  ifup-plusb     ifup-wireless
ifdown       ifdown-ppp       ifup-bnep     ifup-post      init.ipv6-global
ifdown-bnep  ifdown-routes    ifup-eth      ifup-ppp       network-functions
ifdown-eth   ifdown-sit       ifup-ib       ifup-routes    network-functions-ipv6
ifdown-ib    ifdown-Team      ifup-ippp     ifup-sit
ifdown-ippp  ifdown-TeamPort  ifup-ipv6     ifup-Team
ifdown-ipv6  ifdown-tunnel    ifup-isdn     ifup-TeamPort
```

修改ifcfg-ens33文件

```
HWADDR=00:0c:29:cc:16:f0
TYPE=Ethernet
BOOTPROTO=none
IPADDR=192.168.25.133   #改为192.168.1.4 更改的IP必须在192.168.1.1-192.168.1.5的地址段内选择
PREFIX=24
GATEWAY=192.168.25.2    #改为192.168.1.1
DNS1=192.168.25.2       #改为192.168.1.1  首选DNS, 也就是上面图中的路由器IP
DEFROUTE=yes
IPV4_FAILURE_FATAL=yes
IPV6INIT=no
NAME="eth0"
UUID=6e6f9829-0737-4943-ab21-61d6173ba8c4
ONBOOT=yes
LAST_CONNECT=1438160743	
```

