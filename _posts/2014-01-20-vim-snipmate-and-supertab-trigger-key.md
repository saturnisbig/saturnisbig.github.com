---
layout: post
title: "vim snipMate and supertab trigger key"
description: ""
category: tools
tags: [Vim, snipMate, Supertab]
---
{% include JB/setup %}

Start: 2014-01-20 22:45:00
解决了一个问题：

在Vim中同时装了snipMate和Supertab两个插件，在写Python的时候，
每次输入一些snippet然后按tab，结果总是自动换到snippet定义的
下一个位置去，不是我想要的补全函数名之类的。比如：

    def<tab> <1>name<tab>(<2><tab>):
        <3>pass

这个问题是因为snipMate在发现Supertab插件存在的时候，会让
自己优先于Supertab，所以当想用’tab'来补全函数名时，实际上
触发的是snipMate的设置。

如何解决：

修改snipMate的触发键，如何设置呢？只要在.vimrc中添加下面的
内容，即可将snipMate的触发键重新设置为'Ctrl+j'了：

    :imap <C-J> <Plug>snipMateNextOrTrigger
    :smap <C-J> <Plug>snipMateNextOrTrigger

发现自己总是容易将注意力转移到非主要的事情上，这点必须改掉。
还有就是，要解决一个问题，需要耐心，先把问题弄明白后，才能
最快找到解决的办法。
End: 2014-01-20 22:56:45
