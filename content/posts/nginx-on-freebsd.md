+++
date = '2026-10-22T10:12:51+08:00'
description = 'Configure nginx on FreeBSD.'
draft = false
title = 'Nginx on FreeBSD'
tags = ['nginx']
ShowToc = true
ShowBreadCrumbs = true
+++

## Install
```sh
doas pkg install nginx
```

## nginx.conf

I want to serve my blog, my media to a Kodi client, and proxy local llama.cpp.

In `/usr/local/etc/nginx` directory:

nginx.conf
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

blog.conf
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

llama.conf
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

kodi.conf
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
## Test the config
```sh
doas nginx -t
```

If no errors, we can start the server.

## Start service
```sh
doas service nginx enable
doas service nginx start
```

## Create self-signed SSL certificate

```sh
doas mkdir /usr/local/etc/cert
cd /usr/local/etc/cert
doas openssl req -new -newkey rsa:4096 -nodes -keyout nginx.key -x509 -days 365 -out nginx.crt
```

When asked for information:
```
Country Name (2 letter code) [AU]:CN                                       # country code
State or Province Name (full name) [Some-State]:Fujian                     # state
Locality Name (eg, city) []:                                               # city, can by empty
Organization Name (eg, company) [Internet Widgits Pty Ltd]:ASUS DDNS user  # company
Organizational Unit Name (eg, section) []:                                 # department, can by empty
Common Name (e.g. server FQDN or YOUR name) []:your.servername.com         # server's FQDN, important!
Email Address []:                                                          # admin email address, can by empty
```

## Basic auth
```sh
# add user
echo -n 'kodi:' >> .htpasswd
# password
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

    auth_basic   "Restricted Content";
    auth_basic_user_file /usr/local/etc/nginx/.htpasswd;

    location / {
        root   /usr/local/www/kodi;
        autoindex on;
    }
}
```

## Why not Certbot?

Because my ISP block port 80 and 443. I use ASUS DDNS, so I can't use the DNS way either.

## Why HTTP, not WebDAV?

Because I only need to stream my media to a Kodi client on my TV.

## Why HTTP, not SMB?

The same as above. Besides samba is too heavy.

