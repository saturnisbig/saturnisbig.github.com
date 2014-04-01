---
layout: post
title: "python get yesterday and today"
description: ""
categories: [Python]
tags: [python, datetime]
---
{% include JB/setup %}


问题描述：你想获得今天的日期，并以此计算昨天和明天的日期。

这里主要用到了Python `datetime`模块的`date`和`timedelta`两个子类。
解决方法如下：

    import datetime
    today = datetime.date.today()
    delta_day = datetime.timedelta(days=1)
    yesterday = today - delta_day
    tomorrow = today + delta_day
    print 'Today is: ', today
    print 'Yesterday is: ', yesterday
    print 'Tomorrow is: ', tomorrow

