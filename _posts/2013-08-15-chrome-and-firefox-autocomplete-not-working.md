---
author: levin
comments: true
date: 2013-08-15 21:02:28+08:00
layout: post
title: "chrome和firefox中autocomplete属性失效的解决方法"
categories:
- web
tags:
- javascript
---

在写一个登陆页面的时候，打算把表单里input标签的自动完成功能关闭，于是把帐号和密码框的`autocomplete`属性设置为`off`。由于之前登陆选择了记住密码，每次在chrome和firefox打开或者刷新页面的时候都把帐号和密码给自动完成了。<!-- more -->这也就算了，居然在清空了输入框的帐号以后，点击输入框还带有自动完成功能，这说明`autocomplete`功能完全没生效嘛。

然后谷歌之，却没找到真正的解决方法，要么就是打开浏览器设置将自动完成关闭的，要么是承认了这个现象的确存在却没有解决的方法。既然这样，只好自己搞定了，首先我尝试把密码框的`type`属性由`password`改为`text`，此时再刷新或者提交表单后打开此页面，浏览器就不再自动把帐号和密码填充了。看来原因就在于表单中出现了`password`类型的input标签，chrome和firefox是以这个为判断条件来构建记住密码这个功能的。那么怎么办呢，是不能把`password`框改为`text`类型的，不然用户输入密码一看是明文，那就太不对劲了。

不过既然浏览器以`password`类型的input标签为识别标准，那么在输出页面的时候完全可以把这个密码框的`type`设置为`text`，然后在用户点击密码框输密码时把这个密码框的`type`再变回`password`。照着这么一改，果然有效果，chrome和firefox都不会记住密码和自动完成了，这下问题成功解决。
