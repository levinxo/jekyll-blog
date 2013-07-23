---
author: levin
comments: true
date: 2013-01-19 13:52:21+00:00
layout: post
slug: python-urllib2-auto-login-12306
title: 利用python的urllib2库写爬虫自动登录12306
categories:
- 编程语言
tags:
- python
---

HTTP是无状态的协议，一个网站标记用户的标识用的是cookie，即使是服务器里的session，也会在客户端种植一个cookie来指定这个session。因此，要想模拟登录一个网站，就是要跟踪它的cookie(包括接收和发送)，除了跟踪cookie，有的还需要修改ua(User-Agent)，伪装成正常的浏览器。而python的urllib2库在与服务器交互时正好可以自动保存和发送cookie，抓取网页和模拟登录相当的便利。<!-- more -->

要模拟登录12306，需要输入验证码，在登录表单提交时，页面还会去服务器获取一次随机码，然后才会将随机码和用户名、密码、验证码一并提交，这个过程中，需要接收和发送服务器设置的cookie(即同步cookie)才能登录成功。

下面是代码的实现，非常简单，登录成功后就能继续写模拟查票订票的功能了。

{% highlight python %}
    # -*- coding: gb2312 -*-
    # env: python2.7
    
    import urllib2
    from urllib import urlencode
    from cookielib import LWPCookieJar
    from time import strftime, sleep
    from getpass import getpass
    from re import compile
    
    class curl:
    
        def __init__(self):
            self.logindata = {}
            self.username = ''
            self.password = ''
            self.randcode = ''
            self.ifneedrefreshrandcode = True   #重复登录时是否需要重新输入验证码
    
            #在http交互中即时更新cookie
            self.cookiejar = LWPCookieJar()
            cookiesupport = urllib2.HTTPCookieProcessor(self.cookiejar)
            opener = urllib2.build_opener(cookiesupport, urllib2.HTTPHandler)
            urllib2.install_opener(opener)
    
        #生成验证码图片文件
        def getrandcode(self):
            try:
                response = urllib2.urlopen('http://dynamic.12306.cn/otsweb/passCodeAction.do?rand=sjrand')
                imgdata = response.read()
                f = file('./验证码.jpg', 'wb')
                f.write(imgdata)
                f.close()
            except:
                pass
    
        #获取随机码
        def getloginrand(self):
            try:
                response = urllib2.urlopen('http://dynamic.12306.cn/otsweb/loginAction.do?method=loginAysnSuggest')
                re = compile('loginRand":"(\d+)"')
                match = re.search(response.read())
                return match.group(1)
            except:
                pass
    
        #登录动作
        def login(self, loginrand = ''):
            self.logindata['loginRand'] = loginrand                 #随机码
            self.logindata['refundLogin'] = 'N'
            self.logindata['refundFlag'] = 'Y'
            self.logindata['loginUser.user_name'] = self.username   #用户名
            self.logindata['nameErrorFocus'] = ''
            self.logindata['user.password'] = self.password         #用户密码
            self.logindata['passwordErrorFocus'] = ''
            self.logindata['randCode'] = self.randcode              #验证码
            self.logindata['randErrorFocus'] = ''
    
            try:
                req = urllib2.Request('http://dynamic.12306.cn/otsweb/loginAction.do?method=login', urlencode(self.logindata))
                data = urllib2.urlopen(req)
                text = data.read().decode('utf-8').encode('gb2312') #将utf-8解码再编码为gb2312
                return text
            except:
                pass
    
        #将cookie打印出来，登录成功后可以将此cookie代码弄到浏览器的12306页面里，再刷新页面就直接登录成功了
        def getcookie(self):
            text = '/' + '*' * 20 + ' 会话生成日期：' + strftime('%Y-%m-%d %H:%M:%S') + ' ' + '*' * 20
            cookiestring = '/\r\n\r\njavascript:'
            for c in self.cookiejar:
                cookiestring += " document.cookie = '" + c.name + '=' + c.value + '; path=' + c.path + "';"
            cookiestring += ' void(0);'
            f = file('./会话.txt', 'w')
            f.write(text + cookiestring)
            f.close()
    
    def main():
    
        while ch.username == '':
            ch.username = raw_input('输入12306登录的用户名：\n')
        while len(ch.password) < 6:
            print '\r密码：',
            ch.password = getpass('\n')
        print '\r'
    
        if ch.ifneedrefreshrandcode:
            print '正在生成验证码...'
            ch.getrandcode()
            ch.randcode = ''
            while len(ch.randcode) != 4:
                ch.randcode = raw_input('验证码已生成，请查看文件夹下的"验证码.jpg"并输入验证码：\n')
    
        print '正在生成随机码...'
        loginrand = ch.getloginrand()
        print '随机码：' + loginrand
    
        print '登陆中...'
        text = ch.login(loginrand)
    
        reg = compile('var\sisLogin\s?=\strue')
        match = reg.search(text)
        msg = None
        if match:
            reg = compile("var\su_name\s=\s'(.+)'")
            match = reg.search(text)
            if match:
                msg = match.group(1)
                print msg + '，登陆成功'
                ch.getcookie()
                print '会话文件已生成，请查看文件夹下的"会话.txt"'
                raw_input('Press <Enter>')
                return True
        else:
            reg = compile('var\smessage\s=\s"(.+)"')
            match = reg.search(text)
            if match:
                msg = match.group(1)
                print msg
                ch.ifneedrefreshrandcode = False
                if '密码输入错误' in msg or '登录名不存在' in msg or '已经被锁定' in msg:
                    ch.username = ''
                    ch.password = ''
                else:
                    print '5秒后尝试重新登录...'
                    sleep(5)
                main()
                return False
            elif 'images/er/tools_ico2.gif' in text:
                print '验证码错误'
                ch.ifneedrefreshrandcode = True
                main()
                return False
    
        if '/otsweb/images/warn.gif' in text:
            print '系统打烊中...'
            raw_input('Press <Enter>')
        else:
            print '登录失败，5秒后尝试重新登录...'
            sleep(5)
            main()
    
        return True
    
    if __name__ == '__main__':
        ch = curl()
        main()
    
    #end
{% endhighlight %}
