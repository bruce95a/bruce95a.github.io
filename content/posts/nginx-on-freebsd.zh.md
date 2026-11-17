+++
date = '2026-10-22T10:12:51+08:00'
description = '设置FreeBSD上的nginx'
draft = false
title = '在FreeBSD上使用nginx'
tags = ['nginx']
ShowToc = true
ShowBreadCrumbs = true
+++

## 安装nginx
```sh
doas pkg install nginx
```

## 设置nginx

三个目的
1. 博客
2. Kodi媒体库
3. 代理Llama.cpp

在 `/usr/local/etc/nginx` 目录:

更改nginx.conf
```conf

worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;

    keepalive_timeout  65;

    ssl_certificate     /usr/local/etc/cert/nginx.crt;
    ssl_certificate_key /usr/local/etc/cert/nginx.key;
    ssl_protocols       TLSv1.2 TLSv1.3;
    ssl_ciphers         HIGH:!aNULL:!MD5;
    ssl_session_cache   shared:SSL:10m;
    ssl_session_timeout 10m;

    # HTTPS server
    include /usr/local/etc/nginx/blog.conf;
    include /usr/local/etc/nginx/llama.conf;
    include /usr/local/etc/nginx/kodi.conf;
}
```

新建blog.conf
```conf
server {
    listen       443 ssl;
    listen       [::]:443 ssl;
    server_name  your.servername.com;

    location / {
        root   /usr/local/www/blog;
        index  index.html;
    }
}

```

新建llama.conf
```conf
server {
    listen       445 ssl;
    listen       [::]:445 ssl;
    server_name  your.servername.com;

    location / {
        # Proxy all requests hitting this server block to the backend
        proxy_pass http://192.168.1.1:9931;

        # --- Recommended Headers (Crucial for the backend to know who to talk to) ---

        # Passes the original Host header from the client to the backend
        proxy_set_header Host $host;

        # Passes the real IP address of the client to the backend
        proxy_set_header X-Real-IP $remote_addr;

        # Passes the protocol (http or https) to the backend
        proxy_set_header X-Forwarded-Proto $scheme;

        # Passes the hostname (if needed)
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

新建kodi.conf
```conf
server {
    listen       80;
    listen       [::]:80;
    charset      utf-8;

    location / {
        root   /usr/local/www/kodi;
        autoindex on;
    }
}
```
## 测试配置
```sh
doas nginx -t
```

若测试通过，可以启动服务

## 启动服务
```sh
doas service nginx enable
doas service nginx start
```

## 创建自签名SSL证书

```sh
doas mkdir /usr/local/etc/cert
cd /usr/local/etc/cert
doas openssl req -new -newkey rsa:4096 -nodes -keyout nginx.key -x509 -days 365 -out nginx.crt
```

需要这些信息:
```
Country Name (2 letter code) [AU]:CN                                       # 国家代码
State or Province Name (full name) [Some-State]:Fujian                     # 省/州
Locality Name (eg, city) []:                                               # 城市，可空
Organization Name (eg, company) [Internet Widgits Pty Ltd]:ASUS DDNS user  # 公司
Organizational Unit Name (eg, section) []:                                 # 部门，可空
Common Name (e.g. server FQDN or YOUR name) []:your.servername.com         # 域名，重要！
Email Address []:                                                          # 邮箱，可空
```

## 基本的认证
```sh
# 添加用户
echo -n 'kodi:' >> .htpasswd
# 生成密码
openssl passwd -apr1 >> .htpasswd
```
```conf
server {
    listen       80;
    listen       [::]:80;
    listen       445 ssl;
    listen       [::]:445 ssl;
    server_name  your.servername.com;
    charset      utf-8;

    # 增加认证的配置
    auth_basic   "Restricted Content";
    auth_basic_user_file /usr/local/etc/nginx/.htpasswd;

    location / {
        root   /usr/local/www/kodi;
        autoindex on;
    }
}

## 为什么不使用Certbot获取Let's Encrypt的免费证书？

家庭宽带，运营商屏蔽了 80 和 443 端口。使用路由器的DDNS，也没办法使用DNS挑战。

## 为什么用HTTP，而不是WebDAV？

只有Kodi使用，没有上传和更新文件的需求。

## 为什么用HTTP，而不是SMB？

原因同上。除此之外，Samba也更笨重。

