---
author: levin
comments: true
date: 2012-12-21 15:21:14+00:00
layout: post
slug: install-nginx-php-mysql-in-debian
title: Debian下快速安装nginx+php+mysql
categories:
- web
tags:
- linux
- mysql
- nginx
- php
---

首先进行源的准备工作：

        vim  /etc/apt/sources.list

添加这2行地址：

        deb http://packages.dotdeb.org stable all
        deb-src http://packages.dotdeb.org stable all

<!-- more -->
为上面2个源添加key：

        wget http://www.dotdeb.org/dotdeb.gpg
        cat dotdeb.gpg | sudo apt-key add -

更新源：

        apt-get update
        apt-get upgrade

然后安装nginx、php-fpm、mysql，安装mysql的时候可能会提示你设置新密码。

        apt-get install nginx php5-fpm mysql-server-5.1

安装完成以后简单配置下mysql(此步骤可跳过)：

        mysql_secure_installation

先输入刚才设置的密码，如果之前没有设置密码，不输入直接回车，然后：
        
        Set root password? [Y/n] (Y) 如果已经设置过密码了可选n

        Remove anonymous users? [Y/n](是否移出数据库的默认帐户，如果移出，那么在终端中直接输入mysql是会提示连接错误的)Y

        Disallow root login remotely? [Y/n](是否禁止root的远程登录)Y

        Remove test database and access to it? [Y/n] Y
        Reload privilege tables now? [Y/n] Y

然后安装php，可根据需要安装：

        apt-get install php5-cgi php5-cli php5-common php5-curl php5-dev php5-gd php5-idn php5-imagick php5-imap php5-json php5-mcrypt php5-memcache php5-mhash php5-mysql php5-pspell php5-recode php5-snmp php5-suhosin php5-tidy php5-xcache php5-xmlrpc php5-xsl php-pear php-soap

编辑nginx配置文件：

        vim /etc/nginx/nginx.conf
        server {
                listen 80;
                server_name localhost;
                access_log /var/log/nginx/access.log;

                location ~ \.php$ {
                          fastcgi_pass 127.0.0.1:9000;
                          fastcgi_index index.php;
                          fastcgi_param SCRIPT_FILENAME /usr/share/nginx/html$fastcgi_script_name;
                          include fastcgi_params;
                }
        }

配置完成，重启nginx：

        /usr/sbin/nginx -s reload

如果提示：

        **nginx: [warn] conflicting server name "localhost" on 0.0.0.0:80, ignored**

说明localhost这个server\_name被重复绑定了，这是个小警告，不理会也没事，如果想处理，打开下面的文件修改：

        vim /etc/nginx/sites-enabled/default

将下面这一行给注释掉再重启nginx即可。

        **server_name localhost;**






