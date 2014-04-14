---
layout: post
title: "V2EX daily mission auto"
description: "自动领取V2EX每日登录奖励"
categories: [programming]
tags: [python, v2ex mission daily]
---
{% include JB/setup %}

- Start: 2014-04-14 23:36:42
-   End: 2014-04-14 23:55:27
-  Cost: 19M

之前在V2EX上看到有人提建议，“有什么可以练手的Python小脚本实现”，
有人提议说写个自动登录V2EX，领取每日登录奖励的脚本，今天自己也
试着写了一个（当然有参考到网上其他人写的）。

使用到的包：`urllib`、`urllib2`、`re`、`cookielib`、`bs4`

##### 思路：

设置访问需要用到的cookie变量等，构建opener，
模拟访问登录页面:`http://v2ex.com/signin`，设置headers，构建要
提交的表单数据（包括，{'next': '/', 'u': username, 'p': password,
'once': number-to-be-get, 'next': /}），构建Request，通过预先构建
的opener来打开request，至此，登录成功。

使用opener访问mission的页面(http://v2ex.com/mission/daily)，
检查用户是否已经领取了今日奖励，若已领取，则显示用户当前账户余额，
若未领取，则领取今日奖励，并显示当前账户余额。

代码如下：


    #!/usr/bin/env python
    # _*_ coding: utf-8 _*_
    
    import urllib2
    import urllib
    import bs4
    import cookielib
    import re
    
    
    index_url = 'http://v2ex.com'
    login_url = '/signin'
    mission_url = '/mission/daily'
    user_agent = "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 \
            (KHTML, like Gecko) Chrome/27.0.1453.116 Safari/537.36"
    
    # set the opener with cookie support.
    def set_opener():
        """"""
        cj = urllib2.HTTPCookieProcessor(cookielib.CookieJar())
        return urllib2.build_opener(cj, urllib2.HTTPHandler)
    
    opener = set_opener()
    # visit v2ex.com signin link, retrieve the 'once' value, make a post_data
    def get_once():
        """docstring for get_once"""
        url = index_url + login_url
        pat = r'<input type="hidden" value="(\d+)" name="once" />'
        once_regex = re.compile(pat)
        login_page = opener.open(url).read()
        return once_regex.findall(login_page)[0]
    
    def make_post_data(once, username, passwd):
        data = {
                'next': '/',
                'u': username,
                'p': passwd,
                'once': once,
                'next': '/'
        }
        return urllib.urlencode(data)
    
    # value, set header values, login with 'post_data' and 'header', then visit
    def set_headers():
        """"""
        headers = {
                "User-Agent": "UA",
                "Host": "v2ex.com",
                "Origin": "http://v2ex.com",
                "Referer":
                "http://www.v2ex.com/signin"
        }
        return headers
    
    def login(username, passwd):
        once = get_once()
        post_data = make_post_data(once, username, passwd)
        headers = set_headers()
        req = urllib2.Request(
                url = index_url + login_url,
                data = post_data,
                headers = headers
        )
        resp = opener.open(req)
        resp.read()
        print 'Login success.'
    
    # the v2ex.com/mission/daily link, check wether the user already take the 
    def get_coins():
        url = index_url + mission_url
        mission_page = opener.open(url).read()
        pat = r"location\.href = '(.*)';"
        regex = re.compile(pat)
        next_link = index_url + regex.findall(mission_page)[0]
        print next_link
        if '每日登录奖励已领取' in mission_page:
            print '今日奖励已领取'
            show_balance(next_link)
        else:
            resp = opener.open(next_link)
            get_coin_page = resp.read()
            print "领取成功"
            balance_url = index_url + regex.findall(get_coin_page)[0]
            print balance_url
            show_balance(balance_url)
    
    def show_balance(url):
        """"""
        balance_page = opener.open(url).read()
        soup = bs4.BeautifulSoup(balance_page)
        bt = soup.find_all('div', class_="balance_area")
        print bt
        money_tag = bt[1]
        sub_tags = money_tag.contents
        for i in xrange(0, sub_tags):
            if i % 2 == 0:
                money += sub_tags[i].strip()
        #money = money_tag.contents[0].strip() + money_tag.contents[2].strip()
        print "当前账户余额:", money
    
    
    # coins. If not, retrieve the get coin link and make a click on it, else
    # show user the current coins.
    if __name__ == "__main__":
        login('yourusername', 'password')
        get_coins()


##### 碰到的问题

因为之前写一些抓取的脚本，都是现在ipython交互环境里面测试，可以了之后，再写到
代码里，这次实现自动领取奖励的脚本时，一直以为build_opener后，通过opener先进行
登录，后面再用opener来访问领取奖励页面，肯定能成功，结果在iPython里头试了好几次
每次都是回到了要求登录的页面。

后面看了网上别人写的脚本，都是放在一个脚本里面运行的。但是urllib2的build_opener
返回的对象是否包含cookie信息，需要再查看相关资料。

PS：为了写这个脚本，重复登录，登出了好几次网站，被Livid拉黑了...
