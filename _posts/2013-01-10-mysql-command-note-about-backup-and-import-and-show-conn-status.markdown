---
author: levin
comments: true
date: 2013-01-10 16:10:12+00:00
layout: post
slug: mysql-command-note-about-backup-and-import-and-show-conn-status
title: mysql备份和导入以及查看连接情况指令备忘
categories:
- 数据库
tags:
- mysql
---

<!-- more -->

{% highlight sql %}
    ---连接mysql服务器，-h表示主机地址；-P表示端口，一般为3306；-u表示用户名；-p表示输入密码(稍后会提示输入)
    mysql -h localhost -P 3306 -u root -p
        
    ---将某个表的数据(不包括结构)导出到文件
    select * from `table` into outfile 'd:/g.sql'
        
    ---mysql导出数据库，--default-character-set设置导出文件的编码
    mysqldump -h localhost -P 3306 --default-character-set=utf8 -u root -p dbname > bak.sql
        
    ---mysql导入数据库，注意前面的指令不是mysqldump
    mysql -h localhost -P 3306 --default_character_set=utf8 -u root -p dbname < bak.sql
        
    ---mysql查看最大连接数，默认的一般为100
    show variables like '%max_connections%';
        
    ---mysql查看当前打开连接的数量
    show status like '%threads_connected%';
        
    ---mysql查看当前连接数和其当前的行为
    show processlist;
{% endhighlight %}
