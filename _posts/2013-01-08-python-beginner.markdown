---
author: levin
comments: true
date: 2013-01-08 14:26:17+00:00
layout: post
slug: python-beginner
title: 初学python心得
categories:
- 编程语言
tags:
- python
---

最近有点闲，捣鼓了会python，有点点小心得，不过都是些零碎的知识。在此顺便吐槽下貌似gfw把openshift(rhcloud)的22端口给封了，换了好几处地方ssh都登录不上，在朋友的美国vps上ssh到rhcloud却成功登录，真心纠结。好吧，不说这个了，继续正题。<!-- more -->

对于国人来说中文很重要，python对中文的支持还算不错，不过，如果在编码时未声明编码而在代码中带有中文，可是会报错的。

所以在文件的头部应该声明编码：

{% highlight python %}
    """据说此类语句在emacs中也能生效，未测试"""
    # -*- coding: gb2312 -*-
    # -*- coding: utf-8 -*-
{% endhighlight %}

<br />
要保持从别的文件或者网页中抓取的字符串和编码的字符串一致，或者需要输出特定编码的字符串，还应该对相应的字符串进行编码转换：

{% highlight python %}
    string = '需要转换编码的字符串'	#假设此字符串是utf-8编码，在cmd里输出的话需要转换成gb2312
    string.decode('utf-8').encode('gb2312')	#先解码再编码
{% endhighlight %}

<br />
还有就是查找某字符串是否在另一字符串里时有个捷径：

{% highlight python %}
    print 'e' in 'hello'	#True
{% endhighlight %}

in不仅能检查元素是否在元组里，还能检查子字符串是否在某字符串里。

<br />
如果在文件中看到这样的语句，刚开始可能会感觉挺奇怪的：

{% highlight python %}
    if __name__ == '__main__':
	    #some code
{% endhighlight %}

其实这语句意思是，当独立运行(而不是被import)此脚本时(此时__name__等于'__main__')才执行判断里的代码，这样别的脚本import此脚本时此判断里的代码就不会执行了(因为__name__不等于'__main__')。感觉此处还是很灵活的。
