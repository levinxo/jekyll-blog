---
author: levin
comments: true
date: 2013-05-01 11:33:52+00:00
layout: post
slug: carriage-return-and-line-feed
title: '"carriage return"和"line feed"的区别'
categories:
- 未分类
---

carriage return 中文叫回车(真难听)，就是光标回到一行的开头位置。用\r表示。

line feed 中文叫换行，指光标往下移一行。用\n表示。<!-- more -->
两个合起来就是\r\n，即我们平常说的回车符，光标往下一行并回到行首。

* 在win下使用\r\n，16进制为0D0A
* unix和linux下使用\n，16进制为0A
* mac下使用\r，16进制为0D
