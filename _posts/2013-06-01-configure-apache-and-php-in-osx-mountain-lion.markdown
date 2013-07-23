---
author: levin
comments: true
date: 2013-06-01 16:48:37+00:00
layout: post
slug: configure-apache-and-php-in-osx-mountain-lion
title: mac10.8.3配置apache+php5
categories:
- web
tags:
- apache
- mac
- php
---

首先查看apache是否已经默认安装，一般都默认装好了：<!-- more -->

{% highlight bash %}
    sudo apachectl start
{% endhighlight %}

打开浏览器，查看localhost，若显示it works!则已经安装好了apache且已正常工作。

然后编辑apache配置文件：

{% highlight bash %}
    sudo vim /etc/apache2/httpd.conf
{% endhighlight %}

找到#LoadModule php5\_module libexec/apache2/libphp5.so然后去掉之前的注释

配置php文件夹路径(默认的路径为/Library/WebServer/Documents)：

找到DocumentRoot "/Library/WebServer/Documents"和&lt;Directory "/Library/WebServer/Documents"&gt;，将其默认路径改为你以后想放置php脚本的文件夹路径。

然后打开php脚本文件夹，编辑index.php加入

{% highlight php %}
    <?php
    phpinfo();

{% endhighlight %}


最后打开localhost/index.php，完成。
