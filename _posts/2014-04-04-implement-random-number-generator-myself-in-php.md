---
author: levin
comments: true
date: 2014-04-04 00:25:56+08:00
layout: post
title: "在php里编写自己的随机数发生器"
categories:
- 编程语言
tags:
- php
---


前段时间找工作面试，笔试到一道题目：

自己实现php里的[mt_rand()]({{ site.url_finder }}http://www.php.net/manual/function.mt-rand.php)函数。<!-- more -->

想了一下，做了如下回答：

{% highlight php %}

    <?php
    
    function mt_rand_test($min, $max){
        $gap = $max - $min + 1;
        return $min + time()%$gap;
    }
    
    echo mt_rand_test(1, 100);

{% endhighlight %}

利用高值($max)和低值($min)取时间戳的模再加上低值，这样就能获得低值和高值之间的随机数。
怎么样，感觉这个答案还行吧？

其实是错的，因为time()函数返回的时间戳每一秒才会变一次……
也就是说返回的随机数一秒内都是一样的，
是周期性的[伪随机数]({{ site.url_finder }}http://zh.wikipedia.org/wiki/%E4%BC%AA%E9%9A%8F%E6%9C%BA%E6%95%B0)
，而且周期很小。

在加大周期以后，才会有非周期性随机数函数效果。

怎么加大周期呢，秒不够，微秒(百万分之一秒)顶上：

{% highlight php %}

    <?php
    
    function mt_rand_test1($min, $max){
        $gap = $max - $min + 1;
        list($usec, $sec) = explode(' ', microtime(false));
        //注意微秒小数点之后是8位，8位的末两位总是0，需要去掉，否则在$gap等于100的时候在同一秒时间内取的模总是不变的
        $usec = substr($usec, 2, 6);
        return $min + ($usec+$sec) % $gap;
    }
    
    echo mt_rand_test1(1, 100);

{% endhighlight %}

接下来写一个测试函数**分别**(因为`cycle_show()`里的`$cycle`是静态变量)对上面两个函数进行对比：

{% highlight php %}

    <?php
    
    function cycle_show($k){
        static $cycle = array();
    
        if (isset($cycle[$k])){
            $cycle[$k] += 1;
        } else {
            $cycle[$k] = 1;
        }
        return $cycle;
    }

{% endhighlight %}

周期小的随机数函数`mt_rand_test()`：

{% highlight php %}

    <?php
    
    foreach (range(1, 10000) as $v){
        $a = cycle_show(mt_rand_test(1, 100));
        if ($v == 10000){
            ksort($a);
            print_r($a);
        }
    }

{% endhighlight %}

输出结果：

        Array
        (
            [12] => 10000
        )


周期较大的随机数函数`mt_rand_test1()`：

{% highlight php %}

    <?php
    
    foreach (range(1, 10000) as $v){
        $a = cycle_show(mt_rand_test1(1, 100));
        if ($v == 10000){
            ksort($a);
            print_r($a);
        }
    }

{% endhighlight %}

输出结果：

        Array
        (
            [1] => 109
            [2] => 89
            [3] => 108
            [4] => 92
            [5] => 91
            [6] => 98
            [7] => 99
            [8] => 113
            .
            .
            .
            [95] => 96
            [96] => 103
            [97] => 95
            [98] => 101
            [99] => 107
            [100] => 113
        )

所以，在周期拉大以后，随机性会更明显一点，尽管它还是周期性随机数函数。
