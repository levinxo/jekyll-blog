---
author: levin
comments: true
date: 2012-10-26 17:11:37+00:00
layout: post
slug: some-compatible-issues-in-chrome-and-firefox
title: 关于在chrome中获取IMG标签宽高度为0以及jQuery的animate函数改变background-position在firefox中失效的问题
categories:
- web
tags:
- javascript
- jquery
---

在写一个左右滚动图片的效果时，发生了2个比较头疼的问题。记录如下

首先基本思路是在需要滚动的地方加一个包着img标签的div，js拿到img标签的src值赋给div标签的css属性background-image，然后利用jQuery循环切换div的background-position值即可。<!-- more -->

在实现过程中，chrome老是获取到img的宽和高为0，safari浏览器也是如此。后来发现是图片未载入时代码就已经执行了，因此获取到的宽和高为0，给执行函数包个$.load()函数，待img加载完毕再执行代码即可。

然后用此代码在ie又发现了相应的问题，原因还不知道，解决方案就是遇到ie浏览器时，生成个Image()对象，以此来获取img的宽和高。用js怎么判断ie浏览器？使用下面的代码即可：

{% highlight javascript %}
    var ie = ! ','.split(/,/).length;
{% endhighlight %}

获取图片宽高这个问题解决了，在利用jQuery的animate()函数对div的background-position进行改变时却发现此代码在firefox下失效了，在chrome下却正常执行，只好谷歌之，发现原因：firefox不支持background-position-x这个属性，而chrome支持，此属性并不是css标准属性。同时还发现animate()函数不能同时处理赋给background-position两个值，如果在animate()中放置一个值的话，浏览器将默认是x方向的值。这样的代码是错误的：

{% highlight javascript %}
    animate({'background-position': '10 0'})
    animate({'background-position-x': '10'})
{% endhighlight %}

应为：

{% highlight javascript %}
    animate({'background-position': '10'})
{% endhighlight %}

这样问题完全解决了，但是如果需要在上下滚动图片的地方怎么办呢，好像只有为position的y方向上数值改变写一个滚动扩展了，临时解决方案可以使用jQuery的css()函数来代替animate()设置position的值，只是没有了好看的效果。

代码（上下滚动的图片）：

{% highlight html %}
    <div id="img">
        <img src="./img/exa.png">
    </div>
    <script type="text/javascript">
    var ie = ! ','.split(/,/).length;
    $('#img img').load(function(){
        var split = 3;
        var order = 1;
        if (ie) {
            var newImg = new Image();
            newImg.src = this.src;
            var height = newImg.height;
            var width = newImg.width;
        } else {
            var height = this.height;
            var width = this.width;
        }
        $('#img').css('width', width);
        $('#img').css('height', height / split);
        $('#img').css('background-image', 'url(' + this.src + ')');
        setInterval(function(){
            if (order == split) {
                order = 0;
                $('#img').css('background-position', '0 0');
            } else {
                $('#img').css('background-position', '0 -' + height / split * order + 'px');
            }
            order++;
        }, 3000);
    });
    </script>
{% endhighlight %}

代码（左右滚动的图片）：

{% highlight html %}
    <div id="img2">
        <img src="./img/exa2.png">
    </div>
    <script type="text/javascript">
    var ie = ! ','.split(/,/).length;
    $('#img2 img').load(function(){
        var split = 3;
        var order = 1;
        if (ie) {
            var newImg = new Image();
            newImg.src = this.src;
            var height = newImg.height;
            var width = newImg.width;
        } else {
            var height = this.height;
            var width = this.width;
        }
        $('#img2').css('height', height);
        $('#img2').css('width', width / split);
        $('#img2').css('background-image', 'url(' + this.src + ')');
        setInterval(function(){
            if (order == split) {
                order = 0;
                $('#img2').animate({'background-position': 0});
            } else {
                $('#img2').animate({'background-position': '-=' + width / split});
            }
            order++;
        }, 3000);
    });
    </script>
{% endhighlight %}



