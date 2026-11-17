+++
date = '2026-11-15T14:49:41+08:00'
description = '在FreeBSD上搭建Git服务器'
draft = false
title = 'FreeBSD上的Git服务器'
tags = ['Git', 'nginx']
ShowToc = true
ShowBreadCrumbs = true
+++

## 選擇協議

根據 [Git on the Server - The Protocols](https://git-scm.com/book/en/v2/Git-on-the-Server-The-Protocols)，有幾種協議可選。出於方便和安全性考慮，我選擇 SSH。


## 安裝Git
```sh
doas pkg install git
```

## 設定git用户
```sh
doas adduser git
```

> 當被問到使用哪個shell時，輸入 `git-shell`。

## 準備SSH密鑰
```sh
doas mkdir /home/git/.ssh
doas chmod 700 /home/git/.ssh
doas touch /home/git/.ssh/authorized_keys
doas chmod 600 /home/git/.ssh/authorized_keys
```

向 `authorized_keys` 添加密鑰
```sh
doas vi /home/git/.ssh/authorized_keys
```

將所有權者改為 git
```sh
doas chown -R git:git /home/git/.ssh
```

## 準備仓库
```sh
cd /home/nas
mkdir -p repo/project.git
cd repo/project.git
git init --bare

doas chown -R git:git /home/nas/repo
doas ln -s /home/nas/repo /home/git/repo
```

## 從客戶端連接
```sh
git clone git@gitserver:repo/project.git

# 或者
git add remote origin git@gitserver:repo/project.git
```


## 搭建gitweb
```sh
# 安裝軟體包
doas pkg install nginx p5-CGI fcgiwrap

# 連結仓库
doas ln -s /home/nas/repo /usr/local/www/git

# gitweb配置
doas cp /usr/local/etc/git/gitweb.conf.sample /usr/local/etc/gitweb.conf

doas cp -pR /usr/local/share/examples/git/gitweb /usr/local/www/gitweb
```

`/usr/local/etc/gitweb.conf`
```conf
our $projectroot="/usr/local/www/git";
```

`/usr/local/etc/nginx/gitweb.conf`
```conf
server {
    listen       443 ssl;
    listen       [::]:443 ssl;
    server_name  your.servername.com;

    location /gitweb.cgi {
        include /usr/local/etc/nginx/fastcgi_params;
        gzip off;
        fastcgi_param   SCRIPT_FILENAME /usr/local/www/gitweb/gitweb.cgi;
        fastcgi_param   GITWEB_CONFIG   /usr/local/etc/gitweb.conf;
        fastcgi_pass    unix:/var/run/fcgiwrap/fcgiwrap.sock;
    }

    location / {
        root /usr/local/www/gitweb;
        index gitweb.cgi;
    }
}
```

在 `/usr/local/etc/nginx/nginx.conf` 中添加這行
```conf
include /usr/local/etc/nginx/gitweb.conf;
```

## 配置fcgiwrap
```sh
doas sysrc fcgiwrap_enable="YES"
doas sysrc fcgiwrap_user="www"
doas sysrc fcgiwrap_group="www"
doas sysrc fcgiwrap_socket_owner="www"
doas sysrc fcgiwrap_socket_group="www"
doas service fcgiwrap start
```

## 重啟nginx
```sh
doas service nginx restart
```

## 參考

* [Pro Git](https://git-scm.com/book/en/v2): [4. Git on the Server](https://git-scm.com/book/en/v2/Git-on-the-Server-The-Protocols)
* [Server World](https://www.server-world.info/en/): [Git : Install Gitweb](https://www.server-world.info/en/note?os=FreeBSD_15&p=git&f=6)
* [Server World](https://www.server-world.info/en/): [Nginx : Use CGI Scripts](https://www.server-world.info/en/note?os=FreeBSD_15&p=nginx&f=6)
* [Gitweb](https://wiki.archlinux.org/title/Gitweb)
