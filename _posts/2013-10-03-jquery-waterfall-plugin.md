---
author: levin
comments: true
date: 2013-10-03 23:50:12+08:00
layout: post
title: "jquery瀑布流插件"
categories:
- web
tags:
- jquery
---

随着知识的增加，发现不懂的越来越多，想写些东西却有无从下手的感觉。不过总是要记录点什么的，不至于将知识忘的太快。<!-- more -->

花了些时间写了个jquery实现的瀑布流，这个插件可以将数据部分地丢入瀑布流中，也可以对已加入瀑布流的元素进行重新排列。

在index.html中给`window`绑定了两个事件，一个是`scroll`，一个是`resize`。

* `scroll`事件触发时判定是否到达页面底部，调用`ajax`获取数据后将其放入瀑布流中，并将上面看不到的元素`detach`掉，以节省dom空间，当然，往上翻的时候还会将数据还回来
    （此处参考了[360图片搜索瀑布流]({{ site.url_finder }}http://uxc.360.cn/archives/1285.html)的实现思想。此外不小心发现了360图片搜索瀑布流的bug：
    当第一次往下拉页面的时候，不管拉了多少，上面原本应该放入`fragment`的结点都没有放进去，必须往下拉之后再往上拉到顶部才会正常，我的浏览器是Chrome30.0，别的浏览器未试）。
* `resize`事件触发时将对所有的元素重排，这个估计浏览器会有点吃力，因此延迟了2s执行。

在chrome下开发的，写完插件放ie6试了下，居然完美运行没报错，jquery果然很强大。

附：例子代码放在[Github]({{ site.url_finder }}https://github.com/levinxo/serendipity/tree/master/jq_waterfall "jquery waterfall plugin source code destination")

