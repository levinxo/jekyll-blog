---
author: levin
comments: true
date: 2013-07-28 15:08:58+08:00
layout: post
title: "根据jekyll的分页插件编写的主页分页逻辑"
categories:
- web
tags:
- jekyll
---


前几天将博客从wordpress移到了jekyll，期间花了些时间，基本上适应了这个东西。<!-- more -->

因为jekyll是生成静态html文件来形成的整个站点，所以搜索功能是比较难实现的了，等谷歌把这个站点收录的差不多了再弄个自定义搜索引擎吧。
还有wordpress里很简单的分页，到了这里也不得不让人好好研究下。

jekyll的<a href="http://jekyllrb.com/docs/pagination/" rel="nofollow">分页插件</a>是内置的，按照文档上的说明稍微设置下就能用起来。
只是它要么就显示所有的页码数字，要么就只保留上一页、下一页，当页码数很多时是很难受的，所以页码数应该能根据当前页码和总页码数限定在某个范围内显示。

花了些时间自己写了个分页逻辑，因为需求比较简单，没有写成可自定义的分页。
现在是除了第一页、最后一页、上一页、下一页这四个固定页码，会显示10个数字页码（当然你要有那么多页的文章），
其中在当前页码前显示3个页码，当前页码后显示6个页码。
在当前页码的前几页页码不够3个时，若后面存在6个以上的页码会自动补满10个页码，反之亦然。

用liquid写好的分页逻辑放在
<a href="http://git.io/zpcrXA" rel="nofollow">这里</a>。

如果拿来用，最好是改造下，修改相应的标签和class以便于直接使用自己主题的分页样式。

***
更新于2013-10-10：

上面的分页样式是自己写的样式，目前新增了使用bootstrap的分页样式，更契合博客的主题一点。

代码地址在：[这里]({{ site.url_finder }}https://github.com/levinxo/levinxo.github.io/blob/master/_includes/JB/posts_paginator_bs "jekyll主页分页插件")


