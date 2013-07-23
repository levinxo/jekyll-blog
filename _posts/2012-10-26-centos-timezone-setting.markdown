---
author: levin
comments: true
date: 2012-10-26 18:10:11+00:00
layout: post
slug: centos-timezone-setting
title: CentOS系统时区设置
categories:
- Unix/Linux
tags:
- linux
---

在安装完CentOS之后，设置时区为香港时间，却发现比真实的时间多了8小时。

这是因为CentOS默认bios的时间为utc时间，而香港属于东八区，所以显示出来多了8小时。

解决方法（在桌面的环境中设置的）：<!-- more -->

{% highlight bash %}
    vim /etc/sysconfig/clock
    UTC=false #设置硬件时间不与utc时间一致
{% endhighlight %}

设置时钟和NTP服务器时间同步

设置时区为东八区

{% highlight bash %}
    /sbin/hwclock --systohc #设置硬件时间和系统时间一致并校准
{% endhighlight %}

参考资料：

    http://webcache.googleusercontent.com/search?q=cache:ck_R0vZlolsJ:www.osyunwei.com/archives/528.html+&amp;cd=1&amp;hl=zh-CN&amp;ct=clnk

完。
