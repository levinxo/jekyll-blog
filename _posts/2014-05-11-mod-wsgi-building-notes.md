---
author: levin
comments: true
date: 2014-05-11 23:03:11+08:00
layout: post
title: "为Apache和Python安装mod_wsgi"
categories:
- web
tags:
- apache
- python
---

本文记录在Mac OS上为Apache和Python安装mod_wsgi的过程。<!-- more -->

系统：Mac OS X 10.8.5，Apache：2.2.27，Python：2.7.2。

安装mod_wsgi 3.4：

        ./configure --with-apxs=/Users/levin/dev/apache2.2.27/bin/apxs --with-python=/usr/bin/python
        make
        make install

编辑httpd.conf使Apache导入模块mod_wsgi.so以及引入vhost配置文件：

        LoadModule wsgi_module modules/mod_wsgi.so
        Include conf/extra/httpd-vhosts.conf

编辑extra/httpd-vhosts.conf新建项目：

        WSGIScriptAlias /webapp /Users/levin/dev/py/webapp/app.py/
        Alias /webapp/static /Users/levin/dev/py/webapp/static/     #静态文件
        AddType text/html .py
        
        <Directory /Users/levin/dev/py/webapp/>
            Order deny,allow
            Allow from all
        </Directory>

先写个测试脚本app.py

{% highlight python %}

    def application(environ, start_response):
        start_response('200 OK', [('Content-Type', 'text/html')])
        return ['Hello, world.']

{% endhighlight %}

或者使用`web.py`框架：

{% highlight python %}

    import web
    
    urls = (
        '/.*', 'hello',
    )
    
    class hello:
        def GET(self):
            return "Hello, world."
    
    application = web.application(urls, globals()).wsgifunc()

{% endhighlight %}

在浏览器中访问：
http://localhost:*8000*/webapp，看到*Hello, world.*就算安装成功了。

参考：

1. [modwsgi - Python WSGI adapter module for Apache. - Google Project Hosting]({{ site.url_finder }}https://code.google.com/p/modwsgi/)
2. [Getting Started with WSGI - Armin Ronacher's Thoughts and Writings]({{ site.url_finder }}https://lucumr.pocoo.org/2007/5/21/getting-started-with-wsgi/)
3. [Webpy + Apache with mod_wsgi (web.py)]({{ site.url_finder }}http://webpy.org/cookbook/mod_wsgi-apache)

