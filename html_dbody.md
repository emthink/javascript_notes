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
DTD告诉浏览器当前文档用的是什么标记语言，然后浏览器才能正确的根据W3C标准解析文档代码。目前htmlDTD有三种类型：

- Strict DTD：严格的文档类型定义


	不能包含已过时的元素（或属性）和框架元素。
	
- Transitional DTD：过渡的文档类型定义

	能包含已过时的元素和属性但不能包含框架元素。

- Frameset DTD: 框架集文档类型定义

	能包含已过时的元素和框架元素。
	

在html文档中定义DTD就是通过！doctype定义，如下，是一个html4.0的过渡DTDhtml文档：
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
或在html5中：

```

	<!doctype html>
	<html>
		<head>
			<title></title>
		</head>
		<body>
		</body>
	</html>
```
	
## document.documentElement与document.body

- document代表的是整个文档(对于一个网页来说包括整个网页结构)，document.documentElement是整个文档节点树的根节点，在网页中即html标签；
- document.body是整个文档DOM节点树里的body节点，网页中即为body标签元素。

我们常看见如下这种写法获取页面滚动条滚过的长度：

```

	var top = document.documentElement.scrollTop || document.body.scrollTop;
	或
	var top = document.documentElement.scrollTop ? document.documentElement.scrollTop : document.body.scrollTop;
```
在文档使用了DTD时，document.body.scrollTop的值为0，此时需要使用document.documentElement.scrollTop来获取滚动条滚过的长度；在未使用DTD定义文档时，使用document.body.scrollTop获取值。