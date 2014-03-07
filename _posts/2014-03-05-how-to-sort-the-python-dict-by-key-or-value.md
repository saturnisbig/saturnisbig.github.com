---
layout: post
title: "How to sort the Python dict by key or value"
description: ""
categories: [Python]
tags: [sort, python dict]
---
{% include JB/setup %}

如何对Python的字典类型进行排序，返回一个有序的数据？

对字典排序有一下几种方法：

sorted a dict by keys(Python2.4 or greater)

    mydict = {
        '2014-02-03': ['item1', 'item2'],
        '2014-02-05': ['item2-1', 'item2-2']
    }
    for key in sorted(mydict.iterkeys()):
        print "%s: %s" % (key, mydict[key])


sorted by key(Python older than 2.4)

    keylist = mydict.keys()
    keylist.sort()
    for key in keylist:
        print "%s: %s" % (key, mydict[key])


sorted a dict by value(Python2.4 or greater)

    for k, v in sorted(mydict.iteritems(), key=lambda (k, v): (v, k)):
        print "%s: %s" % (k, v)


我需要对数据排序后，在模板中按照排的顺序显示出来：

solution:

    result = []
    for k in sorted(mydict.iterkeys()):
        result.append((k, mydict[k]))
        print "%s: %s" % (k, mydict[k])

