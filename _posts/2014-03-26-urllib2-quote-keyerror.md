---
layout: post
title: "urllib(2) quote KeyError"
description: ""
category: 
tags: [urllib2, quote, KeyError]
---
{% include JB/setup %}

在使用`urllib2.quote`的时候，碰到了下面这个错误：
`<type 'exceptions.KeyError'>: u'\u043f'`，从错误提示的意思
看，应该是编码的问题。我是在Django中使用到的，默认的字符编码
是'utf8'，所以解决上年这个错误的方法是：

    s = u'测试'
    urllib2.quote(s.encode('utf8'))

这个问题，如果自己没记错的话，是第二次碰到了。

参考资料：

1.http://stackoverflow.com/questions/15115588/urllib-quote-throws-keyerror-u-xe9

2.http://stackoverflow.com/questions/5557849/is-there-a-unicode-ready-substitute-i-can-use-for-urllib-quote-and-urllib-unquot
