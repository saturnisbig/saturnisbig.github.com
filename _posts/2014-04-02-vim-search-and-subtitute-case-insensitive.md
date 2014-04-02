---
layout: post
title: "Vim查找与替换字母（不）敏感"
description: ""
categories: [tools]
tags: [Vim, search, subtitue, insensitive]
---
{% include JB/setup %}

Vim默认是对字母大小写敏感的，通过在vimrc中做设置，可以
让Vim在查找与替换的时候忽略大小写，这个设置是:

    :set ignorecase

如果设置了`smartcase`，那么你查找的字符串里如果再包含一个大写字母，
那么就查找的时候就是大小写敏感的，是不是很聪明。

上面忽略大小写的全局设定通常是我们大多数人的需求，但是，如果有时候，
你需要查询大小写敏感的字符串，那么可以使用下面的方法：

在字符串模式前添加`\c`字符串就可以做到在这次查找中忽略大小写

如果添加的是`\C`则又变为了大小写敏感了

比如：

    /\cWord   -- 将匹配 word, Word, WORD, WoRd, etc.
    /\CWord   -- 将只匹配 Word

