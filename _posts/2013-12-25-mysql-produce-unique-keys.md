---
author: levin
comments: true
date: 2013-12-25 23:08:15+08:00
layout: post
title: "利用mysql生成唯一序号"
categories:
- 数据库
tags:
- mysql
---



在数据库分表或者程序自己需要唯一id的情况下，我们需要一个生成唯一id的方案。<!-- more -->

可以编写一个综合时间和某些特征生成唯一id的程序，也可以考虑使用数据库里自增id的特性来实现这个需求，下面举个mysql的例子。

首先创建一个专门生成id的表，其中id字段是主键，replace_key字段为唯一键。

{% highlight sql %}

    CREATE TABLE `ticket` (
        `id` bigint(20) unsigned NOT NULL auto_increment,
        `replace_key` char(1) NOT NULL default '',
        PRIMARY KEY (`id`),
        UNIQUE KEY `replace_key` (`replace_key`)
    ) ENGINE=MyISAM DEFAULT CHARSET=utf8 AUTO_INCREMENT=10001;    
{% endhighlight %}


每次需要生成id时，利用replace into语句生成新的记录将旧的记录替换掉，然后返回此id即可。

{% highlight sql %}

    REPLACE INTO `ticket` (`replace_key`) VALUES ('a');

    SELECT LAST_INSERT_ID();
{% endhighlight %}


来源：[http://code.flickr.net]({{ site.url_finder }}http://code.flickr.net/2010/02/08/ticket-servers-distributed-unique-primary-keys-on-the-cheap/)。





