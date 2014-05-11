---
author: levin
comments: true
date: 2014-05-11 21:38:39+08:00
layout: post
title: "在Mac OS X编译安装Apache2.2和PHP5.5"
categories:
- unix/linux
tags:
- mac
- php
- apache
---

Apache 2.2.27和PHP 5.5.12在Mac OS X 10.8.5编译安装过程：<!-- more -->

在开始之前首先升级Xcode的组件：
preferences => Downloads => 下载Command Line Tools包

然后进入Apache的源码包，对于大部分的源码包都可以使用下面这个指令来查看编译选项：

        ./configure --help

配置编译选项：

{% highlight bash %}

    ./configure --prefix=/Users/levin/dev/apache2.2.27 --enable-modules=most --enable-mods-shared=all

{% endhighlight %}

报错：

        checking for APR-util... yes
        checking for gcc... /Applications/Xcode.app/Contents/Developer/Toolchains/OSX10.8.xctoolchain/usr/bin/cc
        checking whether the C compiler works... no
        configure: error: in `/Users/levin/Downloads/httpd-2.2.27':
        configure: error: C compiler cannot create executables
        See `config.log' for more details

上面的报错是指路径：/Applications/Xcode.app/Contents/Developer/Toolchains/OSX10.8.xctoolchain/usr/bin/cc不存在

解决方法：

{% highlight bash %}

    cd /Applications/Xcode.app/Contents/Developer/Toolchains
    sudo ln -s XcodeDefault.xctoolchain OSX10.8.xctoolchain

{% endhighlight %}

接着编译并安装：

{% highlight bash %}

    make
    make install

{% endhighlight %}

编辑配置文件`httpd.conf`，一般修改监听端口、运行Apache的用户和组、服务器名称、开启gzip等配置。

进入安装好的目录启动Apache:

        ./bin/apachectl start

安装PHP

关于PHP编译选项可以参考：
[PHP: List of core configure options - Manual]({{ site.url_finder }}http://www.php.net/manual/en/configure.about.php)

{% highlight bash %}

    ./configure --prefix=/Users/levin/dev/php5.5.12 --with-apxs2=/Users/levin/dev/apache2.2.27/bin/apxs --with-config-file-path=/Users/levin/dev/php5.5.12/etc --with-openssl --with-zlib --enable-bcmath --with-bz2 --with-curl --enable-ftp --with-gd --enable-gd-native-ttf --with-mhash --enable-mbstring --enable-soap --enable-zip --enable-sockets --with-mysql=/usr/local/mysql-5.6.12-osx10.7-x86_64 --with-mysqli=/usr/local/mysql-5.6.12-osx10.7-x86_64/bin/mysql_config --with-iconv --with-pear --enable-opcache
    make

{% endhighlight %}

整个编译过程需要5-10分钟，视硬件配置而定，可以先去弄杯牛奶喝

发生错误：

        Undefined symbols for architecture x86_64:
          "_res_9_init", referenced from:
              _zif_dns_get_mx in dns.o
              _zif_dns_get_record in dns.o
              _zif_dns_check_record in dns.o
          "_res_9_search", referenced from:
              _zif_dns_get_mx in dns.o
              _zif_dns_get_record in dns.o
              _zif_dns_check_record in dns.o
          "_res_9_dn_skipname", referenced from:
              _zif_dns_get_mx in dns.o
              _zif_dns_get_record in dns.o
          "_res_9_dn_expand", referenced from:
              _zif_dns_get_mx in dns.o
              _php_parserr in dns.o
        ld: symbol(s) not found for architecture x86_64
        collect2: ld returned 1 exit status
        make: *** [libs/libphp5.bundle] Error 1

解决：

{% highlight bash %}

    export LDFLAGS=-lresolv

{% endhighlight %}

错误：

        dyld: Library not loaded: libmysqlclient.18.dylib
          Referenced from: /Users/levin/Downloads/php-5.5.12/sapi/cli/php
          Reason: image not found
        ...
        make: *** [ext/phar/phar.php] Error 133

解决：

{% highlight bash %}

    cd /usr/lib
    ln -s /usr/local/mysql-5.6.12-osx10.7-x86_64/lib/libmysqlclient.18.dylib libmysqlclient.18.dylib

{% endhighlight %}

重新编译并安装：

{% highlight bash %}

    make clean && make
    make install

{% endhighlight %}

安装完成，此时需要从PHP的源码目录copy一份配置文件到安装目录的etc文件夹下面并改名为php.ini，需要更改PHP的配置时就使用该文件即可。

参考：

1. [configure: error: C compiler cannot create executables]({{ site.url_finder }}http://stackoverflow.com/questions/10357804/configure-error-c-compiler-cannot-create-executables)
2. [Building PHP on OS X]({{ site.url_finder }}http://www.devsumo.com/technotes/2013/02/building-php-on-os-x/)

