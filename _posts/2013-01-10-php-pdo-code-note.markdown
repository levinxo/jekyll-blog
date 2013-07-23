---
author: levin
comments: true
date: 2013-01-10 14:39:06+00:00
layout: post
slug: php-pdo-code-note
title: php数据库抽象层PDO小记
categories:
- 编程语言
tags:
- mysql
- php
---

php5以后，多了个PDO数据库抽象层，相比adodb，PDO更效率，而adodb相对来说封装得更好，因此在确认不会更换数据库的情况下，使用PDO是个不错的选择。数据库抽象层对于单纯的php\_mysql函数来说，也是以后的大势所趋。下面是个简单的使用PDO类的例子：<!-- more -->

{% highlight php %}
    <?php
    //数据库配置信息
    $c = 'mysql: host=localhost; port=3306; dbname=user;';
    //设置帐号密码并实例化PDO类
    $db = new PDO($c, 'root', 'password');
    
    $sql = 'select * from `user`';
    
    //一次性获取所有数据
    $handler = $db->query($sql);
    $handler->setFetchMode(PDO::FETCH_ASSOC);	//设置获取数据时为关联数组
    $data = $handler->fetchAll();
    print_r($data);
    
    //一行行地获取数据
    $handler = $db->query($sql);
    $handler->setFetchMode(PDO::FETCH_ASSOC);
    while ($row = $handler->fetch()) {
    	print_r($row);
    }
    //不用销毁实例化的PDO类，我查看过mysql连接池，脚本结束运行，连接会自动释放
    //$db = null;
{% endhighlight %}
