---
author: levin
comments: true
date: 2013-09-03 23:05:29+08:00
layout: post
title: "mysql导出指定字段的数据"
categories:
- 数据库
tags:
- mysql
---

最近需要在mysql中把一个表中的某些字段下的数据导出接着再导入到另外个数据库中，常用的`mysqldump`指令无法选定某个字段导出，只能使用`-w`选项来进行`where`筛选。查了很多相关资料，终于解决了这个问题。<!-- more -->下面把实现的过程总结一下。


首先，创建数据库来做个例子：

{% highlight sql %}

    create table if not exists `score` (
    `id` int(11) unsigned not null auto_increment,
    `name` varchar(16) not null,
    `score` int(3) not null default 0,
    primary key (`id`)
    ) engine=MyISAM default charset=utf8;
    
    insert into `score` (`name`, `score`)values
    ('张三', 80),
    ('李四', 85),
    ('a', 90),
    ('b', 95),
    ('c', 100);

{% endhighlight %}

导出数据：

{% highlight sql %}
    select `name`,`score` from `score` into outfile '/Users/levin/dev/sql/score.sql';
{% endhighlight %}

上面的语句导出的文件是不带字段名的，如果需要带上字段名，将字段名字和结果集`union`一下就好了：
{% highlight sql %}
    select 'name','score' union select `name`,`score` from `score` into outfile '/Users/levin/dev/sql/score.sql';
{% endhighlight %}

将此纯文本的文件导入数据库中：
{% highlight sql %}
    load data infile '/Users/levin/dev/sql/score.sql'
    into table `score`
    character set utf8
    fields terminated by '\t'
    lines terminated by '\n'
    ignore 1 lines
    (`name`, `score`);
{% endhighlight %}

如果需要`into outfile`能生成可以直接执行的语句在导出时就需要利用`concat`函数做处理，注意数据中的`'`需要处理好

        select concat('(\'', replace(name, '\'', '\'\''), '\',\'', score, '\'', '),') from `score` into outfile '/Users/levin/dev/sql/score.sql';

生成的文件内容像这样：

        ('张三','80'),
        ('李四','85'),
        ('a','90'),
        ('b','95'),
        ('c','100'),

这样的文件还需要手动做些处理以实现批量导入的效果，打开文本以后将最后的`,`换成`;`，再在最前面加上语句：
{% highlight sql %}
    insert into `score` (`name`, `score`)values
{% endhighlight %}


