---
author: levin
comments: true
date: 2012-10-29 17:35:40+00:00
layout: post
slug: php-force-download
title: PHP编写下载脚本，可强制下载txt格式文件
categories:
- web
tags:
- php
---

对于普通的txt文件，如果直接跳转的话就是在浏览器中打开，而需要强制下载就需要脚本来帮助了

<!-- more -->

{% highlight php %}
    <?php
    $file = $_GET['file'] ? './downloads/' . $_GET['file'] : null;
    //获取文件的完整名字(包括后缀名，不包括路径)
    $name = basename($file);
    
    if (file_exists($file)) {
    	//任意的二进制数据
    	header('Content-Type: application/octet-stream');
    	//为下载的文件命名
    	header('Content-Disposition: attachment; filename=' . $name);
    	//此步最重要，读取文件，不然下载的文件将是空的。
    	readfile($file);
    }
    
    //over
{% endhighlight %}



