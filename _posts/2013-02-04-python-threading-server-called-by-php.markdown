---
author: levin
comments: true
date: 2013-02-04 17:09:13+00:00
layout: post
slug: python-threading-server-called-by-php
title: php调用多线程python服务器并解析对应python脚本
categories:
- web
tags:
- php
- python
---

一开始就上手各种web框架并不是特别好，因为很多原理层面的东西都很少去了解了。最近在研究python的socket和多线程，于是写了个多线程的socket服务器，能调用相应的脚本来处理自定义的socket请求，客户端用的是php，当然，不一定用php，只要能socket编程即可。<!-- more -->

首先，python服务器需要能完整接收从php发来的socket信息，并从中获取相应的调用信息，因为是纯字符交互，为了方便，将双方的输出转为json字符串。其次为了完整接收信息，还要定义一个字符传输是否完整的标准：在字符串开头定义整个字符串的长度。然后涉及到服务器里动态调用python脚本的问题(因为输入到服务器的参数都是字符串)，此处使用\_\_import\_\_()函数将模块赋值给某一变量并使用exec()函数执行导出结果即可，调用完毕可能还需要释放调用的模块或脚本，使用del语句可达到此目的。

{% highlight python %}
    # -*- coding: utf-8 -*-
    
    import socket
    import threading
    import json
    import sys
    
    HOST = '127.0.0.1'	#绑定监听的ip，此时只有本机可以访问，为空或者0.0.0.0时外界可访问
    PORT = 32123		#绑定监听的端口
    RECVBUFFER = 1024	#socket接收字节流的缓冲大小，单位为字节
    MAXTHREADS = 100	#开启监听的线程数
    RELEASEPKG = True	#是否在某线程执行完毕一次request后释放客户端请求加载的脚本
    APPFOLDER = 'app'	#普通脚本存放的位置，即在此文件夹写各种python脚本等待客户端调用
    
    class Gear(threading.Thread):
    	def __init__(self, socket):
    		#将threading.Thread()作为超(父)类继承并初始化
    		threading.Thread.__init__(self)
    		self.socket = socket
    
    	def run(self):#将需要在子线程里执行的方法写进run()里重载threading.Thread()的run()方法即可
    		while True:
    			self.socket.listen(5)
    			clientsock, clientaddr = self.socket.accept()
    			print 'Got connection from', clientsock.getpeername()
    			print self.getName(), 'is handling this connection.'
    			datasize = clientsock.recv(11)		#接收前11位形如29.00000000的字符串来得知所需接收数据的大小
    			try:
    				datasize = int(float(datasize))
    			except:
    				datasize = 0
    
    			data = ''
    			#当还有数据时，将数据接收并连接给data变量；python没有do...while语句，所以用while+break来控制此流程
    			while True:
    				recv = clientsock.recv(RECVBUFFER)
    				datasize -= RECVBUFFER
    				data += recv
    				if datasize <= 0:
    					break
    
    			try:
    				datamap = json.loads(data)
    			except:
    				datamap = {'entry': '', 'func': '', 'param': []}
    
    			#entry代表某个脚本，func代表需要调用的函数名，param是提供给此函数的参数组
    			entry = datamap['entry']
    			func = datamap['func']
    			param = datamap['param']
    			e = ''
    			try:
    				#将调用的脚本当成模块载入
    				src = __import__(APPFOLDER + '.' + entry, globals(), locals(), -1)
    				if len(param) == 0:
    					try:
    						exec("result = src." + func + "()")
    					except Exception as e:
    						result = []
    				else:
    					#将参数列表序列化为字符串
    					for i in range(0, len(param)):
    						param[i] = str(param[i])
    						paramstring = "','".join(param)
    					try:
    					#执行客户端需要调用的方法并获取结果
    						exec("result = src." + func + "('" + paramstring + "')")
    					except Exception as e:
    						result = []
    				if RELEASEPKG:
    					try:
    					#删除(释放)动态调用的模块
    						exec("del sys.modules['" + APPFOLDER + '.' + entry + "']")
    					except:
    						pass
    			except Exception as e:
    				result = []
    
    			send = json.dumps({'data': result, 'error': str(e)})	#error为捕获app文件夹下python脚本的错误信息
    			#计算输出字符串的长度一起发送给客户端
    			sendsize = len(send)
    			sendsize = str(sendsize) + '.'
    			if len(sendsize) > 11:
    				pass
    			while len(sendsize) < 11:
    				sendsize += '0'
    			send = sendsize + send
    			clientsock.send(send)
    			clientsock.close()
    
    class Engine():
    	def __init__(self):
    		self.socket = None
    
    	def run(self):
    		self.socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    		try:
    			self.socket.bind((HOST, PORT))
    		except:
    			print 'PPY ERROR[PYTHON]: socket.bind() error'
    			raise SystemExit
    		i = 0
    		while i < MAXTHREADS:
    			#实例化子线程并让其开始执行等待客户端调度
    			gear = Gear(self.socket)
    			gear.start()
    			i += 1
    
    if __name__ == '__main__':
    	engine = Engine()
    	engine.run()
    
    #end
{% endhighlight %}

这样，一个小型的调用python脚本的多线程pythonsocket服务器就构造完毕了,如果输出是

        HTTP/1.1 200 OK\r\nContent-Type: text/plain; charset=UTF-8\r\n\r\nhello, world.

就成了一个HTTP服务器。当然也可以直接用浏览器访问这个socket服务器，并修改此脚本把浏览器的输入打印出来，就能对HTTP协议有一个底层上的大概了解了。

其实如此动态地调用python脚本和各种python框架差不多，是框架最初的雏形，在此基础上增加各种api来方便使用的话，就成了一个完整的框架了，当然，这也是为什么python脚本不能像php那样随便扔一个文件夹外部就能访问到，因为单个python脚本是"不具备"输出HTTP头信息的能力的，除非你不嫌麻烦和效率低下调用服务器的CGI并一个个文件地去写头信息。

如果想让脚本后台一直运行，使用

{% highlight bash %}
    nohup python ***.py &
{% endhighlight %}

指令即可。

要停止脚本，查找其进程再终止即可：

{% highlight bash %}
    ps aux | grep python
    kill -s 15 12345	#12345为进程id
{% endhighlight %}

接下来的php客户端脚本就简单多了，只要发送参数并接收结果就完事了：

{% highlight php %}
    <?php
    
    define('PPY_HOST', '127.0.0.1');
    define('PPY_PORT', '32123');
    define('PPY_RECVBUFFER', 1024);	//socket接收数据时缓冲的大小，单位为字节
    
    function exchange($entry='', $func='', $param=array()){	//依次是要访问的python脚本名，脚本里的函数名以及函数的参数
    	if (!isset($entry{0}) || !isset($func{0})){
    		die('PPY ERROR[PHP]: PARAM entry && func must be specified');
    	}
    	if (!is_array($param)){
    		die('PPY ERROR[PHP]: PARAM param must be an array');
    	}
    
    	$c = socket_create(AF_INET, SOCK_STREAM, 0);
    	if (socket_connect($c, PPY_HOST, PPY_PORT) === false){
    		die('PPY ERROR[PHP]: socket_connect() error');
    	};
    	$send = json_encode(array('entry' => $entry, 'func' => $func, 'param' => $param));
    	$sendsize = strlen($send);
    	$sendsize .= '.';
    	if (strlen($sendsize) > 11){
    		die('PPY ERROR[PHP]: Too much data.');
    	}
    	while (strlen($sendsize) < 11){
    		$sendsize .= '0';
    	}
    	$send = $sendsize . $send;
    
    	if (socket_write($c, $send) === false){
    		die('PPY ERROR[PHP]: socket_write() error');
    	}
    
    	if (($datasize = socket_read($c, 11)) === false){
    		die('PPY ERROR[PHP]: socket_read() error');
    	}
    	$data = '';
    	do {
    		if (($recv = socket_read($c, PPY_RECVBUFFER)) === false){
    			die('PPY ERROR[PHP]: socket_read() error');
    		}
    		$datasize -= PPY_RECVBUFFER;
    		$data .= $recv;
    	}
    	while ($datasize > 0);
    	socket_close($c);
    	return $data;
    }
    
    //测试：
    print_r(exchange('h', 'hello'));
    
    //end.
{% endhighlight %}

在python服务器脚本旁边的app文件夹下新建\_\_init\_\_.py和h.py脚本，其中\_\_init\_\_.py留空内容，h.py里面写入hello方法：

{% highlight python %}
    def hello():
	    return 'hello, world.'
{% endhighlight %}

此时目录结构看起来应该是这样的：

![php调用多线程python服务器目录结构]({{ site.img_url }}python-threading-server-file-folder.png)

然后在浏览器里访问php脚本，就能看到hello world啦

![php调用多线程python服务器并解析对应python脚本hello world例子]({{ site.img_url }}python-threading-server-hello-world.png)
