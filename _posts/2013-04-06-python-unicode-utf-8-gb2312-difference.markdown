---
author: levin
comments: true
date: 2013-04-06 16:08:06+00:00
layout: post
slug: python-unicode-utf-8-gb2312-difference
title: python中unicode字符集和utf-8与gb2312编码的区别
categories:
- 编程语言
tags:
- python
---

在win xp上写一个用python实现的文件内容搜索器时，发现编码是个头疼的问题，每个文件的编码都不确定，于是恶补了下这方面的知识，并结合python做了总结。<!-- more -->

首先unicode是种字符表示方式的集合，而utf-8是这种字符集合的计算机编码存储实现方式，简称utf-8编码。其次gb2312也是种计算机字符编码存储实现方式，简称gb2312编码。
	
* 未声明编码的python文件是按ascii码解析的，出现中文会出错；
* 在utf-8编码的python文件中字符串是用utf-8编码的，直接打印到cmd里就是乱码；
* 在utf-8编码的python文件中字符串前加个'u'，则表示此字符串是用unicode字符集表示的，若打印出来，则python会自动转换成cmd能识别的gb2312码；
* 在gb2312编码的python文件中字符串是用gb2312编码的，直接打印到cmd正常；
* 在gb2312编码的python文件中字符串前加个'u'，表现同第三条，结合上条可知unicode和gb2312码可直接输出，python会自动将unicode向某个字符编码实现；
* decode()方法是将指定的编码转为unicode字符表现方式，比如'中文'.decode('utf-8')，而encode()方法是将某字符串的unicode字符表现方式转为相应编码，如'中文'.decode('utf-8').encode('gb2312')。在python中转换编码时，一般都使用unicode字符集作为中间介质。

<br />
下面结合2个python代码的例子来看看它们是怎么表现的

第一个utf-8文件的例子：

{% highlight python %}
    # -*- coding: utf-8 -*-
    
    print '测试'	#乱码
    print u'测试'#正常，unicode输出时python会自动转换成终端能识别的
    
    s = '中文'	#此时s为utf-8编码
    
    if not isinstance(s, unicode):	#若s不为unicode类实例化的对象
    	print s.decode('utf-8'), ' 编码为utf-8'.decode('utf-8')
    
    s = u'中文'	#此时s为unicode字符集
    
    if isinstance(s, unicode):	#若s为unicode类实例化的对象
    	print s, ' unicode字符集'.decode('utf-8')
    
    s = raw_input('请输入：'.decode('utf-8').encode('gb2312'))
    print s	#录入时s为gb2312码
    
    #end
{% endhighlight %}

第二个gb2312文件的例子：

{% highlight python %}
    # -*- coding: gb2312 -*-
    
    print '测试'	#正常
    print u'测试'#正常，unicode输出时python会自动转换成终端能识别的
    
    s = '中文'	#此时s为gb2312编码
    
    if not isinstance(s, unicode):	#若s不为unicode类实例化的对象
    	print s, ' 编码为gb2312'
    
    s = u'中文'	#此时s为unicode字符集
    
    if isinstance(s, unicode):	#若s为unicode类实例化的对象
    	print s, ' unicode字符集'
    	#上一行前一个字符串s能正常输出是因为它为unicode字符集，而后一个字符串能正常输出是因为它为gb2312编码
    
    s = raw_input('请输入：')
    print s	#录入时s为gb2312码
    
    #end
{% endhighlight %}
