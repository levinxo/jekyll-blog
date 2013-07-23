---
author: levin
comments: true
date: 2012-12-19 15:54:09+00:00
layout: post
slug: debian-install-firefox-and-enable-chinese-support
title: debian安装最新firefox与让系统支持中文
categories:
- Unix/Linux
tags:
- linux
---

debian和火狐对于firefox有商标和名称上的争议，在debian下的火狐被换成了iceweasel。虽说iceweasel是火狐的一个分支，但我想用的还是原汁原味的火狐。<!-- more -->

首先在/etc/apt/source.list加入下面一行：

        deb http://downloads.sourceforge.net/project/ubuntuzilla/mozilla/apt/ all main

然后为此源添加认证key：

        apt-key adv --recv-keys --keyserver keyserver.ubuntu.com C1289A29
        apt-get update

安装firefox：

        apt-get install firefox-mozilla-build

使系统支持中文：

可以将windows系统下的字体文件直接复制到debian下的/usr/share/fonts/truetype，我复制的是simsun.ttc。

然后安装中文输入法：

        apt-get install scim
        apt-get install scim-pinyin

安装完成重启就行了。
