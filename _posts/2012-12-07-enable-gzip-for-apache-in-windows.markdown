---
author: levin
comments: true
date: 2012-12-07 18:59:30+00:00
layout: post
slug: enable-gzip-for-apache-in-windows
title: windows下为apache开启gzip
categories:
- web
tags:
- apache
---

发送给客户端的文本数据经过gzip后体积将大大减少，传输速度更快。以此，开启gzip是很有必要的。

windows下为apache开启gzip直接配置配置文件和站点下的.htaccess文件即可，linux下应该也是大同小异(因为只是修改了配置文件)。<!-- more -->

首先打开apache下的conf文件夹httpd.conf文件，将下面两行代码前的#去掉，开启deflate和header模块。deflate是压缩模块，header模块是在http头中加入参数Content-Encoding告诉客户端服务器发送过去的数据是利用什么方式压缩的。

        LoadModule deflate_module modules/mod_deflate.so
        LoadModule headers_module modules/mod_headers.so

再在需要开启gzip的文件夹下的.htaccess文件加入下面的代码：

        <IfModule mod_deflate.c>
        SetOutputFilter DEFLATE				#开启压缩
        SetEnvIfNoCase Request_URI .(?:gif|jpe?g|png)$ no-gzip dont-vary		#不压缩图片
        SetEnvIfNoCase Request_URI .(?:exe|t?gz|zip|bz2|rar)$ no-gzip dont-vary	#不压缩压缩包
        SetEnvIfNoCase Request_URI .(?:pdf|doc)$ no-gzip dont-vary
        AddOutputFilterByType DEFLATE text/*	#压缩形如text/plain text/html text/css之类的文本数据
        #压缩js，xml文件
        AddOutputFilterByType DEFLATE application/javascript application/x-javascript application/xml
        AddOutputFilterByType DEFLATE application/x-httpd-php	#压缩php生成的文档
        </IfModule>
        #over

这样gzip就开启成功了。
