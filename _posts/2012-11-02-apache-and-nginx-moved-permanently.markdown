---
author: levin
comments: true
date: 2012-11-02 20:26:25+00:00
layout: post
slug: apache-and-nginx-moved-permanently
title: apache和nginx重定向(301)
categories:
- web
tags:
- apache
- nginx
---

从seo看，搜索引擎不喜欢抓取到相同内容的东西(<a href="http://en.wikipedia.org/wiki/Duplicate_content" rel="nofollow">Duplicate content</a>)，而在建站的时候，一般example.com和www.example.com访问到的是同样的内容，不利于seo。<!-- more -->

因此，在用户访问example.com时，最好将之重定向至www.example.com，搜索引擎将只会抓取到一份单独的内容。

在apache下的301重定向代码(已验证)：

        #.htaccess文件

        RewriteEngine On
        
        RewriteCond %{HTTP_HOST} ^geeklevin.com$
        RewriteRule (.*)$ http://www.geeklevin.com/$1 [R=301,L]
        
在nginx下的301重定向代码(已验证)：
        
        #nginx.conf 写在server{}块下
        if ($host = 'geeklevin.com') {
        	rewrite ^/(.*)$ http://www.geeklevin.com/$1 permanent;
        }

完。

***
更新于2013-11-06：

上面写的在apache下301重定向代码有点问题，当网址中出现路径时，
路径是不会被传递到新地址上去的，只有query string(即?后面的参数)会被传递，
所以更正如下：

        <IfModule mod_rewrite.c>
        RewriteEngine On

        RewriteCond %{HTTP_HOST} ^geeklevin.com$
        RewriteRule ^.*$ http://www.geeklevin.com%{REQUEST_URI} [R=301,L]
        </IfModule>
        
服务器变量REQUEST_URI包含了请求的path和query string。

