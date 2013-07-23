---
author: levin
comments: true
date: 2013-07-08 18:08:58+00:00
layout: post
slug: charset-encoding-convert-in-vim
title: 用vim对文件进行字符转换
categories:
- Unix/Linux
tags:
- vim
---

fileencoding: 在用vim对文本进行编辑和保存时默认使用的编码。

fileencodings: vim在打开文本时会从其值(一般为utf-8,gb18030,latin-1)中逐个探测适合的字符编码，然后将fileencoding值覆盖为探测到的值。<!-- more -->

在vim中使用:set fileencoding可以查看此文件的字符编码。

若想将文件转为某个编码，可以使用:set fenc=utf-8指令再保存即可。
