---
author: levin
comments: true
date: 2013-03-19 17:30:20+00:00
layout: post
slug: fan-has-higher-speeds-in-debian
title: debian风扇一直很响的原因
categories:
- Unix/Linux
tags:
- linux
---

这几天在用联想y330玩debian，发现风扇一直以最大功率狂转，刚开始还不是很在意，渐渐的让人不免有些烦躁。实在受不了了，寻找原因，才发现是因为未安装ati的官方闭源显卡驱动，默认使用的是开源显卡驱动，估计对风扇的控制不太好，一直最大功率运作，囧。<!-- more -->

于是到ati官网把显卡驱动程序下好，在此：<a href="http://support.amd.com/us/gpudownload/Pages/index.aspx" rel="nofollow">http://support.amd.com/us/gpudownload/Pages/index.aspx</a>

然后解压得到个.run文件，其实是个sh脚本，再使用sh命令运行即可，安装过程是图形界面，很轻松，最后记得重启。

附在此过程中可能用到的指令：

{% highlight bash %}
    lspci | grep VGA
    glxinfo | grep render   #这两个指令都可查看显卡类型
    
    aptitude install linux-headers-$(uname -r)      #下载linux的开发头文件，在驱动程序编译时需要
    
    aticonfig --initial -f  #安装完成以后初始化ati显卡驱动程序
    
    lsmod | grep fglrx      #查看安装模块，我的安装完成以后的驱动模块叫fglrx
    modinfo fglrx           #查看此模块的详情
    
    #end
{% endhighlight %}
