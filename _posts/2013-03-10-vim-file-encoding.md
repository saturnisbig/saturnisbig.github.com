---
layout: post
title: "Vim fileencoding"
description: ""
categories: [tools]
tags: [vim, fileencoding]
---
{% include JB/setup %}


# Vim文件编码问题


在Windows下用GVIM编辑了一个HTML文件，Ubuntu下在gterminal下用vim打开，
文件内的中文呈现乱码。我将乱码的内容删除，并输入中文，在保存文件的时候
出现了“转换错误（Conversion ERROR） ”，无法将编辑后的文件保存。为什么
出现这个问题呢？是不是g-terminal编码设置的问题，可是我的gterminal能够
正常的显示中文，且编码设置为uft-8，所以g-terminal应该不是问题的所在，
于是就发现必须要了解下vim是如何处理编码的问题。从互联网上搜集了相关资料如下：

关于编码 ：gb2312是在ASCII基础上扩展得到的，后来又扩充了gbk和gb18030，其
中最新版的gb18030是05年才release出来，在常用简体中文字上，三种编码是完全兼容的，
只是新的标准增加了很多生僻字、偏旁部首和繁体字。unicode是和ascII完全不同的
一套编码方案，utf8就 是基于这种方案的一种编码，utf8包含有除中文外的其他多国语言，
适合全球使用，所以utf8的体积要比gb系的字符集合大很多，但在中文字方面，
和 gb18030相当，其编码和gb系完全不兼容。

## VIM中相关的选项

1. Vim 有四个跟字符编码方式有关的选项：encoding、fileencoding、fileencodings、
termencoding (这些选项可能的取值请参考 Vim 在线帮助 :help encoding-names)，
它们的意义如下:
    
> encoding: Vim 内部使用的字符编码方式，包括 Vim 的 buffer (缓冲区)、菜单文本、消息文本等。用户手册上建议只在 .vimrc 中改变它的值，事实上似乎也只有在 .vimrc 中改变它的值才有意义。
  fileencoding: Vim 中当前编辑的文件的字符编码方式，Vim 保存文件时也会将文件保存为这种字符编码方式 (不管是否新文件都如此)。
  fileencodings: Vim 启动时会按照它所列出的字符编码方式逐一探测即将打开的文件的字符编码方式，并且将 fileencoding 设置为最终探测到的字符编码方式。因此最好将 Unicode 编码方式放到这个列表的最前面，将拉丁语系编码方式 latin1 放到最后面。
  termencoding: Vim 所工作的终端 (或者 Windows 的 Console 窗口) 的字符编码方式。这个选项在 Windows 下对我们常用的 GUI 模式的 gVim 无效，而对 Console 模式的 Vim 而言就是 Windows 控制台的代码页，并且通常我们不需要改变它。

2. Vim 的多字符编码方式工作方式：
  
- Vim 启动，根据.vimrc 中设置的 encoding 的值来设置 buffer、菜单文本、消息文的字符编码方式。

- 读取需要编辑的文件，根据 fileencodings 中列出的字符编码方式逐一探测该文件编码方式。并设置 fileencoding 为探测到的，看起来是正确的 (注1) 字符编码方式。

- 对比 fileencoding 和 encoding 的值，若不同则调用 iconv 将文件内容转换为 encoding 所描述的字符编码方式，并且把转换后的内容放到为此文件开辟的 buffer 里，此时我们就可以开始编辑这个文件了。注意，完成这一步动作需要调用外部的 iconv.dll，你需要保证这个文件存在于 $VIMRUNTIME 或者其他列在 PATH 环境变量中的目录里。

- 编辑完成后保存文件时，再次对比 fileencoding 和 encoding 的值。若不同，再次调用 iconv 将即将保存的 buffer 中的文本转换为 fileencoding 所描述的字符编码方式，并保存到指定的文件中。同样，这需要调用 iconv.dll

## 解决此问题的方式为：

在.vimrc中设置，“set fileencodings=ucs-bom,utf-8,GB18030,gbk”

其他一些有用的内容如下 ：

查看当前文件编码的方式为：“:set fileencoding”；对文件编码进行转换：“:set fileencoding=utf-8”

外部链接：
[此文讲的内容比较使用](http://lijing00333.wordpress.com/2011/02/08/linux%E4%B8%8B%E5%9F%BA%E4%BA%8Evim%E7%9A%84%E6%96%87%E4%BB%B6%E7%BC%96%E7%A0%81/)
[这篇文章搜集大部分网上资料](http://blog.chinaunix.net/space.php?uid=24173732&do=blog&cuid=2289853)
[这篇文章探讨了浏览器解析网页过程中的编码问题](http://ued.taobao.com/blog/2011/08/26/encode-war/)
