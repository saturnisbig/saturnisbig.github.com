---
layout: post
title: "scp stalled"
description: ""
categories: [tools]
tags: [scp, stalled]
---
{% include JB/setup %}


今天通过`scp`上传文件到VPS上的时候，由于上传的文件比较大，出现了
`scp stalled`，上传速度为0的情况，检索发现，`scp`会在传输文件的时候
占用尽可能多的带宽，如果有任何的延迟（network switch of the firewall）
都会引起TCP链接stalled。

解决方法：
限制传输的带宽，可以使用下面的命令：

    $ scp -l 8192 localFile username@remote-host:~
