---
author: levin
comments: true
date: 2013-10-11 21:42:52+08:00
layout: post
title: "通过条件注释探测IE浏览器"
categories:
- web
---


随手查看了下腾讯微云web页面的源码，发现了一段有趣的代码<!-- more -->：

{% highlight html %}
    <!DOCTYPE html>
    <!--[if lt IE 7 ]> <html class="ie6"> <![endif]-->
    <!--[if IE 7 ]> <html class="ie7"> <![endif]-->
    <!--[if IE 8 ]> <html class="ie8"> <![endif]-->
    <!--[if IE 9 ]> <html class="ie9"> <![endif]-->
    <!--[if (gt IE 9)|!(IE)]>--> <html> <!--<![endif]-->
    </html>
{% endhighlight %}

思考了下，他们这段代码应该是用于探测IE浏览器和版本的吧，感觉很巧妙，免去了分析User Agent的步骤，
接着再用js语句来检测`html`标签的`class`属性就行了：

{% highlight javascript %}
    window.IE = document.getElementsByTagName('html')[0].className;
    window.console && console.log(IE);
{% endhighlight %}

在IE10和之后的版本，微软为了遵循标准，将会忽略这种IE独有的条件注释。

关于详细的IE条件注释介绍可参考：

1. [About conditional comments]({{ site.url_finder }}http://msdn.microsoft.com/en-us/library/ms537512.aspx)
2. [IE10标准模式和Quirks模式中删除了对条件注释的支持]({{ site.url_finder }}http://msdn.microsoft.com/zh-cn/library/ie/hh801214.aspx)

