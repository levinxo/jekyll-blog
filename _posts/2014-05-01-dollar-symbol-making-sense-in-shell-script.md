---
author: levin
comments: true
date: 2014-05-01 01:08:16+08:00
layout: post
title: "Shell指令$符号的变戏法"
categories:
- unix/linux
tags:
- shell
---

在BashShell中`$`符号一般用来指代变量，但是它在括号、花括号等符号的配合下功能也是非常多，处理字符串方面速度也优于awk、sed等外部字符流处理程序，下面做个总结<!-- more -->

{% highlight bash %}
    
    ${var}              变量var的值，与$var一样
    
    $(cmd)	            执行cmd之后的结果值，与`cmd`效果一样
    
    $((1+2))	        同`expr exp`，比较方便的是不用像expr用空格分开每个参数
    
    ${var-$default}		    当变量var不存在时将把$default的值赋给变量var
    ${var:-$default}	    当变量var不存在或者为空时将把$default的值赋给变量var
        
    ${var=$default}		    同${var-$default}
    ${var:=$default}	    同${var:-$default}
    
    ${var+$other}		    如果变量var存在，变量var将被赋值为$other，变量var不存在将被赋值为空串
    ${var:+$other}		    如果变量var存在且不为空串，变量var将被赋值为$other，变量var不存在将被赋值为空串
    
    ${var?$err_msg}		    如果变量var不存在，将打印错误信息$err_msg
    ${var:?$err_msg}		如果变量var不存在或者为空，将打印错误信息$err_msg
    
    ${!a*}		        匹配前缀为a的变量名
    ${!a@}		        同${!a*}
    
    ${#var}		        变量var的长度
    
    ${var:n}	        提取变量第n位之后的字符串
    ${var:n:m}	        提取变量第n位之后的m个字符串
    
    ${var#string}		从变量var的头部寻找最短匹配string的字符串并将其删除
    ${var##string}		从变量var的头部寻找最长匹配string的字符串并将其删除
    ${var%string}		从变量var的尾部寻找最短匹配string的字符串并将其删除
    ${var%%string}		从变量var的尾部寻找最长匹配string的字符串并将其删除
    
    ${var/string/replacement}	用replacement来代替通过string匹配到的第一个子字符串
    ${var//string/replacement}	用replacement来代替通过string匹配到的所有子字符串
    
    ${var/#string/replacement}	如果变量var的开头能匹配上string，那么将用replacement替换掉这个开头
    ${var/%string/replacement}	如果变量var的尾部能匹配上string，那么将用replacement替换掉这个尾部
    
{% endhighlight %}

end.

整理于[linux shell 字符串操作详解]({{ site.url_finder }}http://justcoding.iteye.com/blog/1963463)。



