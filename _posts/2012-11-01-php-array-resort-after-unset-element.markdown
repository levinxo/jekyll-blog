---
author: levin
comments: true
date: 2012-11-01 17:51:35+00:00
layout: post
slug: php-array-resort-after-unset-element
title: php中unset无法重置数组键值的替代处理方式
categories:
- 编程语言
tags:
- php
---

在进行foreach遍历数组时，删除一个数组元素，并需要重置数组的键值。如果是使用unset语句处理，则需要在遍历完成时，再进行一次遍历，手动重置数组的键值。

而array\_splice是这样的一个函数：可以提取数组中指定位置的元素并将之弹出，而被处理的数组将会被重置键值。<!-- more -->

同时还有个重新打乱数组顺序的shuffle函数，将数组打乱以后重新为数组键赋值。

{% highlight php %}
    <?php
    $array = array('a', 'b', 'c', 'd', 'e');
    array_splice($array, 1, 2);	//弹出的同时不为此元素保留键值
    print_r($array);
    //输出Array ( [0] => a [1] => d [2] => e )
    
    $array = array('a', 'b', 'c', 'd', 'e');
    unset($array[3]);
    shuffle($array);	//打乱数组并重新给所有元素键值初始化
    print_r($array);
    //输出打乱的数组，例如Array ( [0] => b [1] => e [2] => a [3] => c )
    //end
{% endhighlight %}
