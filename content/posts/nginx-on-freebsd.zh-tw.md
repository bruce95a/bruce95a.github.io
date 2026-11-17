+++
date = '2026-10-22T10:12:51+08:00'
description = '設定FreeBSD上的nginx'
draft = false
title = '在FreeBSD上使用nginx'
tags = ['nginx']
ShowToc = true
ShowBreadCrumbs = true
+++

## 安裝nginx
```sh
doas pkg install nginx
```

## 設定nginx

三个目的
1. 部落格
2. Kodi媒體庫
3. 代理Llama.cpp

在 `/usr/local/etc/nginx` 目錄:

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
    server_name  your.servername.com;;

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
    server_name  your.servername.com;;

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
## 測試配置
```sh
doas nginx -t
```

若通過測試，可以啟動服務

## 啟動服務
```sh
doas service nginx enable
doas service nginx start
```

## 創建自簽名SSL證書

```sh
doas mkdir /usr/local/etc/cert
cd /usr/local/etc/cert
doas openssl req -new -newkey rsa:4096 -nodes -keyout nginx.key -x509 -days 365 -out nginx.crt
```

需要提供這些信息:
```
Country Name (2 letter code) [AU]:CN                                       # 國家代碼
State or Province Name (full name) [Some-State]:Fujian                     # 省份或州
Locality Name (eg, city) []:                                               # 城市，可以不填寫
Organization Name (eg, company) [Internet Widgits Pty Ltd]:ASUS DDNS user  # 公司
Organizational Unit Name (eg, section) []:                                 # 部門，可以不填寫
Common Name (e.g. server FQDN or YOUR name) []:your.servername.com         # 域名，重要！
Email Address []:                                                          # 可以不填寫
```

## 基本的認證
```sh
# 添加用戶
echo -n 'kodi:' >> .htpasswd
# 生成密碼
openssl passwd -apr1 >> .htpasswd
```
```conf
server {
    listen       80;
    listen       [::]:80;
    listen       445 ssl;
    listen       [::]:445 ssl;
    server_name  your.servername.com;;
    charset      utf-8;

    # 增加認證的配置
    auth_basic   "Restricted Content";
    auth_basic_user_file /usr/local/etc/nginx/.htpasswd;

    location / {
        root   /usr/local/www/kodi;
        autoindex on;
    }
}
```

## 爲何不使用Certbot獲取Let's Encrypt的免費證書？

运营商屏蔽了 80 和 443 端口。使用路由器的DDNS，也没辦法使用DNS挑戰。然而Certbot需要這三種之間的其中一種。

## 爲何使用HTTP，而不是WebDAV？

只有Kodi使用，沒有上傳和更新文件的需求。

## 爲何使用HTTP，而不是SMB？

原因同上。除此之外，Samba也更笨重。

