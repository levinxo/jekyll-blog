---
author: levin
comments: true
date: 2013-07-02 16:38:16+00:00
layout: post
slug: mysql-update-records-when-insert-batch-on-duplicate-key
title: mysql批量插入时更新而不插入重复记录
categories:
- 数据库
tags:
- mysql
---

最近有个定时批量导入和更新用户信息的需求，而且用户量挺大的。这样看来，php是比较难弄了，搞不好入库到一半就挂了，而python又没有直接连mysql的类库，需要自行下载安装MySqldb模块，虽然会用，但现网没这个环境。其实看到现网的python环境是2.7.3的时候我已经很感动了，然后有搜过使用socket直接和数据库交互，无奈暂时还没找到相关资料，估计可行性比较低，时间上不允许。<!-- more -->

这样看来只有shell能较好的解决这个问题了，首先定时去ftp获取对应的用户文件，然后拆解数据再执行。最开始想的是一行行地从文本中获取用户数据然后生成sql语句插入到数据库中，如果重复，则更新对应用户数据，可是shell获取mysql返回数据检查用户是否存在实在麻烦，而且这样做的话一条用户记录就要连接一次数据库，因为在循环前将数据库连接好在循环内和数据库交互好像不是那么容易，在谷歌也没搜到有效的方法。

目前使用shell脚本导入用户数据进库有三点需求：

* 尽量不要一条条信息处理的时候导入数据库，因为在shell里每次和数据库交互都要连接一次数据库，数据多了要悲剧；
* 由于shell中获取mysql返回值很麻烦，所以应该设置trigger或者使用duplicate key以及replace语句在入库时检查是否数据已存在，若存在则更新或者替换；
* 在入库时最好批量插入，而不是一条条的insert。

为了解决上面三个问题，查找了些资料，以前都没写过shell，有点头大，不过写下来感觉shell还是挺好写的

* 在一条条处理用户信息的时候先将信息写成零散的sql语句放进一个临时文件，处理文本完毕以后再对零散的sql语句进行拼接和处理再统一导入数据库中；
* trigger有试过，但是感觉灵活性不大，而且容易遗忘掉。而replace into语句会在发现重复数据记录时将记录删除再写一条新记录，多次进行这个操作时表需要重新索引，不利于性能。所以这次使用的duplicate key语句，在插入时，有主key或者唯一的key重复的话将更新此条记录而不进行插入操作；
* 虽然第一个办法解决了在循环中重复连接数据库的问题，但是在一次连接中有太多的单条insert语句效能也是比较低的，所以需要结合duplicate key语句批量插入和更新。

下面是在批量插入时检查若有重复记录则进行更新的mysql语句，其中mobile为唯一索引

{% highlight sql %}
    INSERT INTO user (mobile,user_data) VALUES
    ('13xxxxxxxxx','some info...'),
    ('13xxxxxxxxx','some info...')
    ON DUPLICATE KEY UPDATE
    user_data=VALUES (user_data);
{% endhighlight %}
