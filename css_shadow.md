---
layout: post
title: New Post
permalink: new-page.html
description: Some Description
date: 2016-01-29 15:41:20 +08:00
tags: "some tags here"
---

# CSS3之阴影效果box-shadow

本篇，我们要讲的是CSS3中经常被使用来开发页面效果的box-shadow属性和text-shadow属性。

在前端开发过程中，我们常常会看见这种效果：

![box-shadow阴影效果图](http://blog-resource.bj.bcebos.com/photos/2016_01/shadow1.png)

```

	<!DOCTYPE html>
	<html lang="en">
	<head>
		<meta charset="UTF-8">
		<title>Shadow</title>
		<style>
			.box {
				height: 100px;
				width: 200px;
				border: 1px ridge silver;
			}
			.shadow1 {
				box-shadow: 4px 4px 6px 1px gray;
			}
		</style>
	</head>
	<body>
		<div class="box shadow1"></div>
	</body>
	</html>
```

这种阴影效果就是使用CSS3box-shadow属性实现。

## box-shadow

> box-shadow属性向框元素添加一个或多个阴影效果。

- 语法：

	`box-shadow: [inset] x-offset y-offset [blur] [spread] [color];`
	
- 属性

属性         |说明
---------- | ----------
inset         |可选，将默认的外阴影设置为内阴影。
x-offset    |必需，水平方向阴影偏移量，可为负值。
y-offset    |必需，垂直方向上阴影偏移量，可为负值。
blur          | 可选 ，阴影模糊半径，不可为负值。
spread     |可选，阴影延展半径，可为负值。
color        |可选，阴影颜色

