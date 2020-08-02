---
title: Nginx配置支持Https访问资源
date: 2020-08-02 15:58:38
update: 2020-08-02 15:58:38
categories: Nginx
tags: [nginx]
---

### Nginx配置代理指定端口

浏览器访问`http://www.686china.xyz`，请求发送到指定服务器；

`Nginx`监听`80`端口，根据`server_name`匹配到对应的`server`配置，从而转发到`proxy_pass`配置的本机`8090`端口；

```text
http {

    # 支持上传文件大小
    client_max_body_size 10M;

    server {
        listen 80;
        server_name www.686china.xyz 686china.xyz;
        # 支持上传文件大小
        client_max_body_size 10M;
        location / {
            # 支持上传文件大小
            client_max_body_size 10M;
            proxy_set_header HOST $host;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://127.0.0.1:8090/;
        }
    }
}
```

### Nginx支持Https访问

在腾讯域名申请`ssl`证书后，下载第三方证书相关文件，目录格式如下

![企业微信20200802112910](https://www.686china.xyz/upload/2020/08/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A120200802112910-98292ae25a6a4cd9bf124f005c23e29a.png)

用到`Nginx`目录下的`xxx.crt`和`xxx.key`文件

#### 安装ssl工具

```sh
#yum install openssl
#yum install openssl-devel
```

#### 配置Nginx

把`.crt`和`.key`文件上传到`nginx`安装目录下录的`certs`目录，`certs`目录是自定义创建的。

```test
server {
    listen  443 ssl;
    # 修改为自己的域名
    server_name 86china.xyz;
    # 修改为自己的文件路径
    ssl_certificate      /etc/nginx/certs/www.686china.xyz.crt;
    # 修改为自己的文件路径
    ssl_certificate_key  /etc/nginx/certs/www.686china.xyz.key;
    ssl_session_timeout  5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
    ssl_prefer_server_ciphers on;

    location / {
        root   html;
        index  index.html index.htm;
        client_max_body_size 10M;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
        proxy_set_header HOST $host;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://127.0.0.1:8090/;
    }
}
```


支持`ssl`的`nginx.conf`完整文件内容

```text
user  nginx;
worker_processes  1;
error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;
events {
    worker_connections  1024;
}
http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    keepalive_timeout  65;

    include /etc/nginx/conf.d/*.conf;


    # ========= 关注配置项 begin ========
    # 上传大小
    client_max_body_size 10M;

    server {
        listen  443 ssl;
        server_name 86china.xyz;
        ssl_certificate      /etc/nginx/certs/www.686china.xyz.crt;
        ssl_certificate_key  /etc/nginx/certs/www.686china.xyz.key;
        ssl_session_timeout  5m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
        ssl_prefer_server_ciphers on;
        location / {
            root   html;
            index  index.html index.htm;
            client_max_body_size 10M;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "Upgrade";
            proxy_set_header HOST $host;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://127.0.0.1:8090/;
        }
    }
    # ========= 关注配置项 end ========
}
```

#### 测试https安全性

<a href="https://myssl.com">SSL/TLS安全评估报告</a>

![企业微信20200802114632](https://www.686china.xyz/upload/2020/08/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A120200802114632-768b00a4232a44f5b4055f8c00d7fb3b.png)


