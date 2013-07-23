---
author: levin
comments: true
date: 2012-12-16 17:06:23+00:00
layout: post
slug: debian-pppoe-reports-error-network-is-down
title: Debian pppoe拨号上网设置和报错network is down处理方法
categories:
- Unix/Linux
tags:
- linux
---

安装完了Debian，由于使用的是pppoe，因此得自己设置拨号上网功能。当然，此环境下是不能用apt-get指令的。
首先去http://packages.debian.org/squeeze/pppoeconf下载相应架构的包和一些依赖包，如果在安装时失败，一般都是因为缺少相应的依赖包，来此处找到相应的包下载就行了。<!-- more -->

将这些.deb文件放在一个U盘里，挂载到debian下，我挂载的是/tmp/pkg文件夹。

        mount /dev/sdb1 /tmp/pkg
        cd /tmp/pkg
        dpkg -i pppoeconf_1.19_all.deb

然后会告知需要的包，将那些依赖的包一一装上即可。安装pppoeconf完成以后输入pppoeconf配置即可，一般来说可以选择自动拨号。下面是一些常用的命令：

        pon dsl-provider		#开启pppoe连接
        poff					#关闭pppoe连接
        plog					#查看pppoe连接日志
        ifconfig ppp0			#查看第一个pppoe连接的状态

如果在连接时发现连接日志报错：Network is down，那么编辑/etc/network/interfaces文件：

        auto eth0
        #iface eth0 inet manual
        iface eth0 inet dhcp		#将manual改为dhcp开机时自动获取地址

然后重新启动网络：

        /etc/init.d/networking stop
        /etc/init.d/networking start

可参考此处：http://unix.stackexchange.com/questions/31607/pppoe-reports-network-is-down
