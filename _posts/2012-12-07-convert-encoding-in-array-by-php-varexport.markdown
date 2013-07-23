---
author: levin
comments: true
date: 2012-12-07 19:44:27+00:00
layout: post
slug: convert-encoding-in-array-by-php-varexport
title: 巧妙使用php的varexport()函数为数组转换编码
categories:
- web
tags:
- php
---

在写网络爬虫时，会遇到网页编码的问题，国内通常的网页编码是GB2312和UTF-8。通常本人喜欢使用UTF-8，因为在生成json字符串的时候只能使用utf-8。

如果是纯字符串使用iconv()函数可以转换不同的编码文字，但是这样比较容易出错，而且无法直接转换数组内容的编码。<!-- more -->

因此，要做些巧妙的处理，php有个函数var\_export()，用于将变量转换为形如array(0 => a)的合法php字符串。

这样可以先将需要转换编码的数组用var\_export()函数输出，然后使用iconv()函数转换编码，转换完成以后再利用eval()函数执行此字符串即可返回编码转换完成以后的数组。

代码实现如下：

{% highlight php %}
    <?php
    function output($arr = array()) {
    	return eval('return ' . iconv('GB2312', 'UTF-8', var_export($arr, true)) . ';');
    }
    //end
{% endhighlight %}
