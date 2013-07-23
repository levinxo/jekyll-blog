---
author: levin
comments: true
date: 2012-11-21 05:45:03+00:00
layout: post
slug: javascript-compare-arguments-list
title: 编写javascript获取不定参数数量的参数中最大值参数的函数
categories:
- 编程语言
tags:
- javascript
---


依此类推可将-Infinity的负号去掉此函数就变为求一堆参数中最小值的函数了。

<!-- more -->

{% highlight javascript %}
    function max() {
        var maxval = -Infinity;     //js中定义的无穷小
        for (var i = 0; i < arguments.length; i++) {       //遍历此函数获取到的参数
            maxval = arguments[i] > maxval ? arguments[i] : maxval;
        }
        return maxval;
    }
    alert(max(1, 2, -8, 99));
    //输出99
{% endhighlight %}


