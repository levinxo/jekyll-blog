---
author: levin
comments: true
date: 2013-11-14 22:34:22+08:00
layout: post
title: "ruby的blocks和迭代器"
categories:
- 编程语言
tags:
- ruby
---

Block是一种可以和方法调用相关联的代码块。

Block的代码放置在花括号或者do..end之间，一般单行block用花括号，多行block用do..end<!-- more -->：

{% highlight ruby %}
    { puts 'hello' }
    do
      #..sth
    end
{% endhighlight %}

将block放在方法调用处的后面，就可以实现关联，例如：

{% highlight ruby %}

    def hello
      puts 'hello, 1'
    end
    
    hello { puts 'hello, 2' }   #输出hello, 1。没有输出block里的内容是因为hello方法只是关联了block，但并未调用block
    
    def hello
      puts 'hello, 1'
      yield
      yield
    end
    
    hello { puts 'hello, 2' }   #输出hello, 1和两次hello, 2。yield语句调用了方法相关联的block

    def hello
      yield('levin')
    end
    
    hello { |name| puts name }  #竖线称为管道符，管道符中间为参数，多个参数之间可以用逗号隔开

{% endhighlight %}

###迭代器

先举个例子：

{% highlight ruby %}

    [1, 2, 'a', 'b'].each { |ele| puts ele }

{% endhighlight %}

上面是Array的each迭代器，在each方法内部将每个元素作为参数传给相关联的block。

更多的例子：

{% highlight ruby %}

    (3..5).each { |i| puts i }      #输出3 4 5
    ['a', 'b', 1, 2].each { |ele| puts ele.succ }     #输出b c 2 3，其中succ方法返回某字符的下一位

{% endhighlight %}

###事务block

block一般和迭代器一起使用，但是block还有别的用处。block可以用来包含必须运行在事务控制环境下的代码。

例如打开一个文件，写入，再关闭此文件，但是我们觉得文件应该自己打开自己，再自己来关闭自己，即让文件管理自己的生命周期。这时候block就派上用场了：

{% highlight ruby %}

    def open_and_process(*args)
      f = File.open(*args)
      yield f
      f.close
    end
    
    open_and_process('test_file') do |f|
      while line = f.gets
        puts line
      end
    end

{% endhighlight %}

在上面，文件首先在`open_and_process`方法中被打开，然后yield调用block并将文件对象作为参数传递过去，block执行完毕返回后再关闭文件。
通过这种方式，打开和关闭文件的责任从使用者身上转移到了文件本身。

其实ruby的File类里的类方法open支持这样的形式。当open方法和一个block关联时，open会自动执行打开和关闭文件的操作，并调用block。
单独使用open方法时，open将直接返回文件对象，此时文件关闭操作需要使用者自己进行。

一个方法如何判断自己有关联的block？`block_given?`方法可以判断是否有block和某方法关联，下面是改进的`open_and_process`方法：

{% highlight ruby %}

    def open_and_process(*args)
      ret = f = File.open(*args)
      if block_given?
        ret = yield f
        f.close
      end
      ret
    end
    
    #关联block
    open_and_process('test_file') do |f|
      while line = f.gets
        puts line
      end
    end
    
    #单独调用
    f = open_and_process('test_file')
    while line = f.gets
      puts line
    end
    f.close

{% endhighlight %}

整理于[Programming Ruby中文版(第2版)]({{ site.url_finder }}http://book.douban.com/subject/2032343/)。
block在此书中介绍的比较零散，于是总结了下，也加入了自己的领悟。

