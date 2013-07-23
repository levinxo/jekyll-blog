---
author: levin
comments: true
date: 2013-01-09 14:55:59+00:00
layout: post
slug: dichotomy-in-python
title: python二分查找算法实例
categories:
- 算法
tags:
- algorithm
- python
---

<!-- more -->

{% highlight python %}
    """
    二分查找算法python实例
    --------------------------------------
    在python里，最简单的实现方法是下面这句，亦可在字符串里查找子字符串
    if num in tuple:
        print 'ok'
    --------------------------------------
    下面的代码是单纯从算法角度考虑
    """
    # -*- coding: utf-8 -*-
    
    class Dichotomy():
    
        def __init__(this, thetuple):
            this.thetuple = thetuple
            this.begin = 0
            this.end = len(thetuple) - 1
            this.middle = 0
    
        def find(this, num):
            this.middle = (this.begin + this.end) / 2
    
            if num < this.thetuple[this.middle]:
                this.end = this.middle - 1
            elif num > this.thetuple[this.middle]:
                this.begin = this.middle + 1
            else:
                return [this.middle, num]
    
            if this.begin > this.end:
                return None
            return this.find(num)
    
    def example(thetuple, num):
        dichotomy = Dichotomy(thetuple)
        result = dichotomy.find(num)
    
        if result is None:
            print 'not found.'
        else:
            print result[1], 'is matched at', result[0]
    
    if __name__ == '__main__':
        example([2, 7, 8, 15, 30, 56, 68, 99], 8)
    
    #end
{% endhighlight %}
