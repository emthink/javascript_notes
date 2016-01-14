---
layout: post
title: New Post
permalink: new-page.html
description: Some Description
date: 2016-01-14 13:22:52 +08:00
tags: "some tags here"
---
# HTML之多列布局CSS实现

在网页布局中非常经典的

## inline-block与多列布局

inline-block是内将元素样式指定为行内内联样式。

> CSS中通过display:inline-block对一个对象指定inline-block属性，可以将对象呈递为内联对象，但是对象的内容作为块对象呈递。

在生成网页导航列表时，常常使用display:inline-block;实现；在多列布局中，其也有一定效果：

```	

	<!doctype html>
	<html>
	<head>	
		<style>
		.left2 {
	            width: 200px;
	            height: 100px;
	            background-color: green;
	            display: inline-block;
	        }
	        .right2 {
	            width: 60%;
	            height: 100px;
	            background-color: red;
	            display: inline-block;
	        }
	        </style>
	<head>
	<body>
		<h2>行内布局使用inline-block</h2>
	      <div class="left2">Left</div>
	      <div class="right2">Right</div>
	</body>
	</html>
```

## 浮动float与多列布局

float 属性定义元素在哪个方向浮动。

> 使用float设置元素浮动后，元素会脱离文档流，左右浮动，而浮动元素原所在位置由后续非浮动元素填充，若浮动元素与后续非浮动元素发生重叠，则浮动元素将覆盖非浮动元素。

```

	<!doctype html>
	<html>
	<head>	
		<style>
		.left2 {
	            width: 200px;
	            height: 100px;
	            background-color: green;
	            display: inline-block;
	        }
	        .right2 {
	            width: 60%;
	            height: 100px;
	            background-color: red;
	            display: inline-block;
	        }
	        </style>
	<head>
	<body>
		<h2>行内布局使用inline-block</h2>
	      <div class="left2">Left</div>
	      <div class="right2">Right</div>
	</body>
	</html>
```

### 浮动float与margin负边距实现多列布局

### 浮动float与margin正边距实现多列布局