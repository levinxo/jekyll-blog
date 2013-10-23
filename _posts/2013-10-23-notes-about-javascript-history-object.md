---
author: levin
comments: true
date: 2013-10-23 20:32:23+08:00
layout: post
title: "javascript里history对象的一些笔记备忘"
categories:
- 编程语言
tags:
- javascript
---


前段时间，自己写了个<a href="https://whois.sinaapp.com" rel="nofollow">查询域名whois</a>的小工具，托管在新浪sae上。
本来每次查询页面都是重新载入的，后来考虑到效率问题，使用了ajax，但是却没办法查看上一条查询记录。
如果在url里使用#号利用hash值的话对于搜索引擎收录又不太友好（虽然这个网址目前还没有被收录）。

后来用了javascript里history库中的两个函数外加一个监听事件实现了这个需求。下面梳理下思路供日后查用。<!-- more -->

####window.history.pushState()方法

这个方法用于新添加一条历史记录，例如：

{% highlight javascript %}
    window.history.pushState({}, null, 'a.html');
{% endhighlight %}

第一个参数和第二个参数可以传入null，第三个参数就是需要变成的url地址，这个地址必须是同源的。
第一个参数可以放入一个对象，浏览器会将此对象变量标记到这个历史记录中，
当从别的页面再次回到这个页面的时候，浏览器调用`onpopstate`事件将此变量当成参数传入。

这里可以在改变url的同时进行ajax的请求，将页面要改变的内容填充好，这样一个新的页面就产生了(从外表来看)。


####window.onpopstate事件

当上面产生了一条历史记录的时候，比如a页面的记录，而用户此时跳到了b页面，如果用户返回上一页（a页面），
浏览器将会触发`onpopstate`事件（前提是用户定义了这个函数用于接收浏览器的调用），
而且会将用户之前在调用`pushState()`方法时给的第一个参数绑定到`event.state`中去，类似：

{% highlight javascript %}
    window.onpopstate = function(event){
        console.log(event.state);
    }
{% endhighlight %}

这里就用于监听获取浏览器发来的`state`值，然后程序再判断这个值来确定载入那些内容以恢复之前在这个页面所看到的东西。

####window.history.replaceState()方法

这个方法用于替换一条历史记录，例如：

{% highlight javascript %}
    window.history.replaceState({}, null, 'a.html');
{% endhighlight %}

这个函数用于将当前的历史记录替换，比如当用户直接在浏览器输入一个地址时，
页面载入完成后，就将页面中某部分的标示放入第一个参数中让浏览器存起来，
待从别的页面返回到这个页面时，这个页面也拥有了监听`onpopstate`事件的资格，
并利用之前保存的标示来重新载入特定部分的内容以符合展示逻辑（注：在firefox的地址栏中直接打开一个网页时，是不会调用`onpopstate`事件的，而chrome会调用）。


####总结

javascript的history对象里这几个方法还是不错的，可以满足在不重载页面的情况下更新页面，
而且对搜索引擎友好，因为在浏览器地址栏中直接打开页面内容也是会完整显示出来的。

使用此对象需要注意的是浏览器的兼容性，不支持的浏览器应该将之直接导向目标页面，
类似下面的语句可以判断浏览器是否支持这两个新的方法：

{% highlight javascript %}
    //这应该算是jQuery中提到的特征侦测(feature detection)吧，
    //jQuery在1.9版本以后不再支持$.browser属性来判断浏览器了，因为仅靠判断UA有时候是不太准确的
    
    window.state_support = !!(window.history.pushState && window.history.replaceState);

{% endhighlight %}



参考资料：

1. [操纵浏览器的历史记录 - DOM | MDN]({{ site.url_finder }}https://developer.mozilla.org/zh-CN/docs/DOM/Manipulating_the_browser_history)
2. [window.onpopstate - DOM | MDN]({{ site.url_finder }}https://developer.mozilla.org/zh-CN/docs/DOM/window.onpopstate)
3. [jQuery.browser | jQuery API Documentation]({{ site.url_finder }}http://api.jquery.com/jQuery.browser/)

