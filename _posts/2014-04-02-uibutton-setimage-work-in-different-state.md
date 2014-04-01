---
layout: post
title: "UIButton setImage在不同的状态下设置是否显示"
description: ""
categories: [ios-dev]
tags: [UIButton, setImage]
---
{% include JB/setup %}

在做斯坦福2013 Iphone应用开发练习时，有个要求是设置UIButton的背景图片。
提示使用`setImage:(UIImage *)image forState:`来设置，但是，这么设置之后，
UIButton在selected状态下也将图片显示了出来。

网上搜索了下，说是，如果你设置了UIButton在normal状态下的图片而没有设置其
它状态如`selected`或者`disabled`等，那么在这些状态下，默认使用的是`normal`
下的设置，所以当点击UIButton的时候，图片并没有消失。

解决方法是：
1. 先创建一张透明的1像素的图片，使用setImage为将其设为其他状态下的显示图片
可以参考[这里](http://stackoverflow.com/questions/17373238/uibutton-only-setimage-on-uicontrolstatenormal/17373801#17373801)

2. 判断UIButton所处的状态，将setImage的image设为nil

        if (!btn.isSelected && btn.isEnabled) {
            [btn setImage: backImage forState:UIControlStateNormal];
        } else {
            [btn setImage: nil forState:UIControlStateNormal];
        }


