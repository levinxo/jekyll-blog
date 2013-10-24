---
author: levin
comments: true
date: 2013-10-20 12:21:28+08:00
layout: post
title: "为什么qq空间好友的图片会显示自己的呢称"
categories:
- web
---



偶尔会发现qq空间好友动态别人转载的图片日志里自己的昵称和头像被展示了出来，感觉挺有趣的。

这是怎么做到的呢<!-- more -->，用Chrome开发者工具查看，图片是链接到一个脚本去的，
由此推测，这个脚本应该是拿到了我的某些信息。

首先，不可能发生了`CSRF`，因为请求的地址是外部的。
其次，`XSS`的可能性也比较小，因为图片标签容纳了那个脚本地址，
而图片标签在目前比较高级的浏览器下调用`javascript`会被block掉，
就算真的是植入脚本，难度也不低。
再仔细看，页面对此图片发送的请求头中只有`Referer`带有我的qq信息，类似：

        Referer: http://user.qzone.qq.com/qq号码/infocenter

看来访问的脚本是根据`referer`来获取qq号码，然后再通过腾讯一些公开的接口获取对应用户的昵称、头像和所在地等信息。

试着用curl构造这个`referer`来访问那张图片所在的地址：

{% highlight bash %}
    $ curl -H "Referer: http://user.qzone.qq.com/qq号码/infocenter" 此处是图片的链接 > qzone.png
    $ open qzone.png
{% endhighlight %}

打开图片后显示的果然是空间里看到的那张图。

