+++
date = '2026-11-15T14:49:41+08:00'
description = 'Setup Git Server on FreeBSD'
draft = false
title = 'Git Server on FreeBSD'
tags = ['Git', 'nginx']
ShowToc = true
ShowBreadCrumbs = true
+++

## Choose Protocols

According to [Git on the Server - The Protocols](https://git-scm.com/book/en/v2/Git-on-the-Server-The-Protocols), there are several protocols. For convenience and security, I choose SSH.


## Set up Git
```sh
doas pkg install git
```

## Set up git user
```sh
doas adduser git
```

> When asked which shell, type `git-shell`.

## Prepare ssh keys
```sh
doas mkdir /home/git/.ssh
doas chmod 700 /home/git/.ssh
doas touch /home/git/.ssh/authorized_keys
doas chmod 600 /home/git/.ssh/authorized_keys
```

add keys to `authorized_keys`
```sh
doas vi /home/git/.ssh/authorized_keys
```

change owner to git
```sh
doas chown -R git:git /home/git/.ssh
```

## Prepare repo
```sh
cd /home/nas
mkdir -p repo/project.git
cd repo/project.git
git init --bare

doas chown -R git:git /home/nas/repo
doas ln -s /home/nas/repo /home/git/repo
```

## From a client
```sh
git clone git@gitserver:repo/project.git

# or
git add remote origin git@gitserver:repo/project.git
```


## Set up gitweb
```sh
# install packages
doas pkg install nginx p5-CGI fcgiwrap

# link the repo
doas ln -s /home/nas/repo /usr/local/www/git

# gitweb config
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

add this line to `/usr/local/etc/nginx/nginx.conf`
```conf
include /usr/local/etc/nginx/gitweb.conf;
```

## Config fcgiwrap
```sh
doas sysrc fcgiwrap_enable="YES"
doas sysrc fcgiwrap_user="www"
doas sysrc fcgiwrap_group="www"
doas sysrc fcgiwrap_socket_owner="www"
doas sysrc fcgiwrap_socket_group="www"
doas service fcgiwrap start
```

## Restart nginx
```sh
doas service nginx restart
```

## References

* [Pro Git](https://git-scm.com/book/en/v2): [4. Git on the Server](https://git-scm.com/book/en/v2/Git-on-the-Server-The-Protocols)
* [Server World](https://www.server-world.info/en/): [Git : Install Gitweb](https://www.server-world.info/en/note?os=FreeBSD_15&p=git&f=6)
* [Server World](https://www.server-world.info/en/): [Nginx : Use CGI Scripts](https://www.server-world.info/en/note?os=FreeBSD_15&p=nginx&f=6)
* [Gitweb](https://wiki.archlinux.org/title/Gitweb)
