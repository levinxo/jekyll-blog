---
author: levin
comments: true
date: 2012-11-22 07:17:25+00:00
layout: post
slug: javascript-reload-and-instance-constructor
title: javascript中函数返回函数，重载以及对象实例化相关
categories:
- 编程语言
tags:
- javascript
---

第一个是最常见的，普通的函数，函数返回的是number类型的值

<!-- more -->

![第一个例子变量test获取到的是函数的返回值，类型是number]({{ site.img_url }}js-simple-function.png)

第二个是函数中返回函数的函数，并且此函数的toString方法被重载了，因此在直接输出test值的时候其返回的就是重载以后的toString函数给的返回值

![第二个例子变量test获取到的是函数的返回值，类型是function，并且此函数的toString方法被重载了]({{ site.img_url }}js-function-in-function.png)

第三个函数无返回值，因此试图直接获取此函数的返回值时只会获取到undefined，所以只有实例化此函数，将之变为对象，就能调用其函数体内各种属性和方法了，其toString方法也被重载了

![第三个例子函数无返回值，或者说其返回值为undefined，因此必须使用new实例化此函数]({{ site.img_url }}js-instance-constructor.png)
