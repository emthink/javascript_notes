---
layout: post
title: New Post
permalink: new-page.html
description: Some Description
date: 2016-01-19 11:59:16 +08:00
tags: "some tags here"
---

# CSS之定位机制
CSS 有三种基本的定位机制：普通文档流、浮动和绝对定位。

## 普通文档流
在普通文档流(即未使用浮动和固定绝对定位时的默认定位方式)中，块级元素一个一个从上到下排列，而行内元素，在一行内，按从左到右排列。

## 浮动float
在浮动定位中，使用float设置标签元素向左或向右浮动，float值有none 或 left 或right：

![](http://blog-resource.bj.bcebos.com/photos/2016_01/css_float.png)

## 绝对定位
绝对或固定元素会生成一个块级框，而不论该元素本身是什么类型。相对定位元素会相对于它在正常流中的默认位置偏移。

### absolute	
生成绝对定位的元素，相对于 static 定位以外的第一个父元素进行定位。
元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行设置，偏移时不影响常规流中的任何元素，z-index可设置其显示层级优先度。
### fixed	
生成绝对定位的元素，相对于浏览器窗口进行定位。
元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行设置，偏移时不影响常规流中的任何元素，z-index可设置其显示层级优先度。
### relative	
生成相对定位的元素，相对于其正常位置进行定位，参照自身在常规流中的位置通过 "left", "top", "right" 以及 "bottom" 属性进行设置。
因此，"left:120" 会向元素在常规流中的位置 LEFT 位置添加 120 像素。
### static	
默认值，没有定位，元素出现在正常的流中（忽略 top, bottom, left, right 或者 z-index 声明）。
### inherit
规定应该从父元素继承 position 属性的值。