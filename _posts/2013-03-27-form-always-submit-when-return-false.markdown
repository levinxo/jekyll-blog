---
author: levin
comments: true
date: 2013-03-27 16:28:19+00:00
layout: post
slug: form-always-submit-when-return-false
title: form表单绑定onsubmit事件返回false却一直提交的问题
categories:
- web
tags:
- javascript
---

前几天开发时，写了个表单验证函数，并将函数绑定到表单的onsubmit事件上。这样表单在提交时数据是否符合要求就能通过函数验证了，函数返回true时提交，返回false时不提交并做相应提示。<!-- more -->我在函数里写了几个正则用于判断，当预想是返回false时，表单却一直提交，为何return false不执行呢。纠结了很久，终于在表单提交的一瞬间看到了firebug里红色的错误信息一闪而过。原来是函数运行错误中断时，onsubmit默认返回的是true，而返回ture时表单会提交，几乎看不到函数报的错误，因为脱离了页面firebug也刷新了。

此错误感觉比较典型，不小心就会犯而且原因及其隐蔽，之前对此问题也搜过谷歌，却没找到解决的方法。
