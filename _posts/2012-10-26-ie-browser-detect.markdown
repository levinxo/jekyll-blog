---
author: levin
comments: true
date: 2012-10-26 18:13:57+00:00
layout: post
slug: ie-browser-detect
title: IE系列浏览器判断，包含IE9
categories:
- web
tags:
- javascript
---

<!-- more -->

{% highlight javascript %}
    var ie = ! ','.split(/,/).length;
{% endhighlight %}


之前使用的：

{% highlight javascript %}
    var ie = ! + [1, ];
{% endhighlight %}


不能用于判断IE9

摘自：http://blog.csdn.net/satans18/article/details/5502129
