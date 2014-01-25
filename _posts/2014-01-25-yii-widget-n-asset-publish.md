---
author: levin
comments: true
date: 2014-01-25 21:25:19+08:00
layout: post
title: "yii框架widget和注册asset的例子"
categories:
- web
tags:
- php
---

yii里的挂件(widget)是个好东西，重用方便、移动灵活，多处会用到的代码可以写成一个widget来统一管理。

yii的asset管理也是比较方便的，可以将一个js或者css插件，甚至是js和css混合的插件弄成一个模块再发布到资源文件夹下，免除了自己手动分类管理js和css文件的步骤。<!-- more -->

下面举一个将
[my97 datepicker]({{ site.url_finder }}http://www.my97.net/)
插件发布到asset下并封装成一个widget的例子。

首先在项目的protected文件夹下建立widgets文件夹，接着在widgets下建立我们需要的插件文件夹，例如dp。文件目录结构如下：

        -protected
          -widgets
            -dp
              -my97
                -此处放入my97 datepicker的文件包
              -views
                -index.php
              -DpWidget.php

再编辑DpWidget.php文件：

{% highlight php %}

    <?php
    class DpWidget extends CWidget {
    
        public function init(){}
    
        public function run(){
            $this->render('index');
        }
    }

{% endhighlight %}

因为只是调用js和css插件，所以我们只需要直接调用视图文件即可，接着在视图文件里将datepicker打个包发布：

{% highlight php %}

    <?php
    /**
     * my97日期控件widget
     */
    $my97_path = Yii::app()->assetManager->publish(
        dirname(dirname(__FILE__)).'/my97/'
    );
    
    Yii::app()->getClientScript()->registerScriptFile($my97_path.'/WdatePicker.js');

{% endhighlight %}

值得注意的是在publish方法里参数是datepicker的整个文件夹，yii会遍历这个文件夹并将下面的js、css和图片等文件一一注册。
在注册完毕后，需要将datepicker其中的主js文件引用出来完成对datepicker的调用，此时执行yii的引用js脚本函数即可。widget封装完成。

最后，可以在项目里调用这个widget啦：

{% highlight php %}

    <?php
    $this->beginWidget('application.widgets.dp.DpWidget');
    $this->endWidget();

{% endhighlight %}

虽然yii的资源管理很方便，但是在注册资源多了以后也可能会发生效率低下的问题，基本上原因在于每次调用`assetManager`的`publish`方法时都强制将文件拷贝到资源文件夹下了。
当然，开发的时候，这样比较方便，改动了js、css文件能马上看出效果，但是在线上环境时就加大了服务器的负担。
要解决这个问题只要将`CAssetManager`类里的`publish`方法的第四个参数`$forceCopy`设置为`YII_DEBUG`常量就好了，在生产环境时强制复制资源文件，在线上环境不强制复制。








