---
author: levin
comments: true
date: 2012-11-26 15:56:46+00:00
layout: post
slug: centos-install-nginx-uwsgi-python
title: Centos下最简化编译配置安装nginx+uwsgi+python
categories:
- web
tags:
- linux
- nginx
- python
- uwsgi
---

用python做web应用最麻烦的还是配置服务器了，此话不假，光中间件就有好几种选择，fastcgi、wsgi、uwsgi，难免让人眼花缭乱。
而听说uwsgi的效率是fastcgi和wsgi的10倍，因此初学python的我就有点跃跃欲试了，打算在centos下搭建个nginx+uwsgi+python玩玩。<!-- more -->

下面是本人经过google和亲身实践所得：

准备工作：

        yum install python-devel libxml2-devel python-setuptools zlib-devel wget pcre-devel gcc make

编译安装nginx：

{% highlight bash %}
    cd /tmp
    wget http://nginx.org/download/nginx-1.2.5.tar.gz
    tar -zxvf nginx-1.2.5.tar.gz
    ./configure				//此处可能会提示有些library未安装，安装完再运行此命令检查即可
    make;make install		//确认无误，编译安装
{% endhighlight %}

编译安装uwsgi:

{% highlight bash %}
    wget http://projects.unbit.it/downloads/uwsgi-1.4.2.tar.gz
    tar -zxvf uwsgi-1.4.2.tar.gz
    cd uwsgi-1.4.2
    python setup.py build
    make
    mv uwsgi /usr/bin		//将编译好的文件移动到此处
{% endhighlight %}

配置nginx

{% highlight bash %}
    vim /usr/local/nginx/conf/nginx.conf
{% endhighlight %}

在server下的location下增加以下2行

        location / {
        	uwsgi_pass 127.0.0.1:9001;
        	include uwsgi_params;
        }

编辑python测试文件

{% highlight bash %}
    vim /var/www/index.py
{% endhighlight %}



{% highlight python %}
    def application(env, start_response):
    	start_response('200 OK', [('Content-Type','text/html; charset=iso-8859-1')])
    	return 'Hello, world'
{% endhighlight %}

启动nginx和uwsgi

{% highlight bash %}
    /usr/local/nginx/sbin/nginx
    uwsgi -s 127.0.0.1:9001 --wsgi-file /var/www/index.py
{% endhighlight %}

Centos系统放在了虚拟机里，我用winxp母机，firefox17.0查看的页面，结果是挺让人吃惊的，单纯的helloworld几乎不耗时间，如图

![nginx+uwsgi+python测试，速度快的惊人]({{ site.img_url }}python-uwsgi-nginx-test.png)

如果是使用django等python框架的，相关的配置信息会更容易找些。

参考自：
http://projects.unbit.it/uwsgi/wiki/Quickstart
http://messense.me/nginx-and-uwsgi-install-on-centos.html
