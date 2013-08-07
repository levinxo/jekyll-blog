---
author: levin
comments: true
date: 2013-08-07 22:08:08+08:00
layout: post
title: "利用python的socket库模拟post、put、delete请求"
categories:
- web
tags:
- python
---


HTTP1.1中有8种请求方式，包括GET、POST、HEAD、CONNECT、TRACE、OPTIONS、PUT、DELETE，其中前四种是最常用的。今天用python模拟了下GET、POST、PUT、DELETE方法。

在所有的请求中，必须带有http头`Host`，比如请求`127.0.0.1:8000`，则`Host`头要写为`Host: 127.0.0.1:8000`。在每个头之间用换行来间隔，头和body之间使用两个换行来间隔。<!-- more -->

###GET方法

GET请求最简单，参数串跟在请求的url之后，用`?`指示参数串开始，GET请求时是不用带有body的。

一个完整的GET请求如下：

        GET /?param1=a&param2=b HTTP/1.1\r\nHost: *.*.*.*\r\n\r\n

###POST方法

POST请求则需要在头中声明body带的参数的类型，一般为`application/x-www-form-urlencoded`，并且在头中明示body数据的长度，body中的数据为urlencode过的参数串，和GET方法url问号之后的参数一样的格式

一个完整的POST请求如下：

        POST / HTTP/1.1\r\nHost: *.*.*.*\r\nContent-Type: x-www-form-urlencoded\r\nContent-Length: 17\r\n\r\nparam1=a&param2=b

###PUT方法

PUT请求若只是纯参数不需要带有`Content-Type`头，但是需要`Content-Length`头指明body的长度。

一个完整的PUT请求如下：

        PUT / HTTP/1.1\r\nHost: *.*.*.*\r\nContent-Length: 17\r\n\r\nparam1=a&param2=b

###DELETE方法

DELETE请求也同样不需要带有`Content-Type`头，但也需要指明body的长度。

一个完整的DELETE请求如下：

        DELETE / HTTP/1.1\r\nHost: *.*.*.*\r\nContent-Length: 17\r\n\r\nparam1=a&param2=b

###实例

下面是用python的socket库写的这4个方法请求实例：

{% highlight python %}

    import socket
        
    param_data = 'param1=a&param2=b'
    param_lenth = str(len(param_data))
    
    
    request_str = '''GET /?'''+param_data+''' HTTP/1.1\r\nHost: *.*.*.*\r\n\r\n'''
    
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.connect(('127.0.0.1', 80))
    sock.send(request_str)
    data = sock.recv(4096)
    sock.close()
    print 'GET method fetch data:'
    print data.split('\r\n\r\n')[1]
    
    
    request_str = '''POST / HTTP/1.1\r\nHost: *.*.*.*\r\nContent-Type: application/x-www-form-urlencoded\r\nContent-Length: '''+param_lenth+'''\r\n\r\n'''+param_data
    
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.connect(('127.0.0.1', 80))
    sock.send(request_str)
    data = sock.recv(4096)
    sock.close()
    print 'POST method fetch data:'
    print data.split('\r\n\r\n')[1]
    
    
    request_str = '''PUT / HTTP/1.1\r\nHost: *.*.*.*\r\nContent-Length: '''+param_lenth+'''\r\n\r\n'''+param_data
    
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.connect(('127.0.0.1', 80))
    sock.send(request_str)
    data = sock.recv(4096)
    sock.close()
    print 'PUT method fetch data:'
    print data.split('\r\n\r\n')[1]
    
    
    request_str = '''DELETE / HTTP/1.1\r\nHost: *.*.*.*\r\nContent-Length: '''+param_lenth+'''\r\n\r\n'''+param_data
    
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.connect(('127.0.0.1', 80))
    sock.send(request_str)
    data = sock.recv(4096)
    sock.close()
    print 'DELETE method fetch data:'
    print data.split('\r\n\r\n')[1]

{% endhighlight %}

server是用php写的，用于接收这四种方法发来的参数并打印，其中`php://input`中保存了只读的客户端请求body中的raw data，如果请求头中不包含`Content-Length`，则`php://input`的值将为空。`parse_str`函数的作用是将query字符串拆解开成为一个一维数组并将这个数组赋到$param这个变量中。

{% highlight php %}
    <?php
    if ($_SERVER['REQUEST_METHOD'] === 'GET'){
        print_r($_GET);
    } else {
        parse_str(file_get_contents('php://input'), $param);
        print_r($param);
    }

{% endhighlight %}



