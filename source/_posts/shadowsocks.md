---
title: shadowsocks vpn
date: 2018-10-13 18:49:59
update: 2018-10-13 18:49:59
categories: vpn
tags: [vpn]
---

### linux搭建vpn

#### 设置服务器入站和出规则

![1](https://volc1612.gitee.io/blog/images/\shadowsocksvpn/a.png)

![1](https://volc1612.gitee.io/blog/images/\shadowsocksvpn/b.png)

添加规则，所有流量，来源为任何位置，出站和入站一样的规则，点击保存
![1](https://volc1612.gitee.io/blog/images/\shadowsocksvpn/c.png)

设置完成后就可以ping通IPv4公有IP了。

#### 设置root密码和安装shadowsocks

***添加root账号并设置密码***

```base
[ec2-user@ip-172-31-18-100 ~]$ sudo su
[root@ip-172-31-18-100 ec2-user]# sudo passwd root
Changing password for user root.
New password: 
BAD PASSWORD: The password is shorter than 8 characters
Retype new password: 
passwd: all authentication tokens updated successfully.
[root@ip-172-31-18-100 ec2-user]# vim /etc/ssh/sshd_config
[root@ip-172-31-18-100 ec2-user]# sudo /sbin/service sshd restart
Redirecting to /bin/systemctl restart sshd.service
[root@ip-172-31-18-100 ec2-user]# sudo service ssh start
Redirecting to /bin/systemctl start ssh.service
Failed to start ssh.service: Unit not found.
[root@ip-172-31-18-100 ec2-user]# /etc/init.d/sshd start
bash: /etc/init.d/sshd: No such file or directory
[root@ip-172-31-18-100 ec2-user]# systemctl restart sshd.service

[root@ip-172-31-18-100 ~]# ssserver -c ~/shadowsocks_conf.json -d restart
INFO: loading config from /root/shadowsocks_conf.json
2018-10-30 14:24:55 INFO     loading libcrypto from libcrypto.so.10
stopped
started
```

***设置服务器ssh客户端连接***

```bash
[root@ip-172-31-18-100 ec2-user]# vim /etc/ssh/sshd_config
```

修改`PasswordAuthentication`为`yes`，重启服务配置。

```bash
[root@ip-172-31-18-100 ec2-user]# sudo /sbin/service sshd restart
Redirecting to /bin/systemctl restart sshd.service
```

#### linux安装shadowsocks客户端

##### 安装

```bash
[root@ip-172-31-18-100 ec2-user]# yum install python-pip
[root@ip-172-31-18-100 ec2-user]# pip install shadowsocks
[root@ip-172-31-18-100 ec2-user]# vim ~/shadowsocks_conf.json
```

##### 配置shadowsocks账号和密码

```json
{
    "server":"0.0.0.0",
    "port_password": {
        "8388": "1234567",
        "8389": "volc123"
    },
    "local_address": "127.0.0.1",
    "local_port":1080,
    "timeout":600,
    "method":"aes-256-cfb",
    "auth": true
}
```

##### 启动shadowsocks客户端

```bash
[root@ip-172-31-18-100 ec2-user]# ssserver -c ~/shadowsocks_conf.json -d start
```

参考地址： 
https://www.tyrion.wang/2017/02/04/VPN%E6%90%AD%E5%BB%BA-%E4%BA%9A%E9%A9%AC%E9%80%8AEC2-Shadowsocks/

