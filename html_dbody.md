---
layout: post
title: New Post
permalink: new-page.html
description: Some Description
date: 2016-01-25 15:03:48 +08:00
tags: "some tags here"
---
# 漫谈document.documentElement与document.body

在前端开发中，我们经常需要获取网页中滚动条滚过的长度，获取该值的方式一般通过scrollTop属性，如：document.body.scrollTop，是不是还经常看见document.documentElement.scrollTop，这两者都是经常用来获取文档滚动条滚过长度值的方式，他们又有什么区别呢？待下文，细细道来：

## DTD
DTD告诉浏览器当前文档用的是什么标记语言，然后浏览器才能正确的根据W3C标准解析文档代码。

- Strict DTD：严格的文档类型定义


	不能包含已过时的元素（或属性）和框架元素。
	
- Transitional DTD：过渡的文档类型定义

	能包含已过时的元素和属性但不能包含框架元素。

- Frameset DTD: 框架集文档类型定义

	能包含已过时的元素和框架元素。
	
如下，是一个过渡DTDhtml文档：

```
	
	<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

	<html>
		<head>
			<title></title>
		</head>
		<body>
		</body>
	</html>
	
```
	
## document.documentElement与document.body
document代表的是整个文档(对于一个网页来说包括整个网页结构)，document.documentElement是整个文档节点树的根节点，在网页中即html标签；document.body是整个文档DOM节点树里的body节点，网页中即为body标签元素。

