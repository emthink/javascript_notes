---
layout: post
title: Scope 
permalink: js_scope.md
description: introduce the JavaScript Scope 
date: 2016-01-01 09:55:23 +08:00
tags: "share javascript notes"
---

#JavaScript之作用域与作用域链
今天是2016的第一天，我们得扬帆起航踏上新的征程了。此篇阐述JavaScript中很重要的几个概念：作用域与作用域链及相关知识点。

我们先从变量与作用域的行为关系开始讨论。

##变量作用域
JavaScript中，变量有全局变量及局部变量之分，而能定义变量作用域的语块只有函数。与局部变量有关的一种有趣特性，在此处不得不谈--变量提升。

###变量提升
变量提升为何物？

> JavaScript的变量声明会被提升到它们所在函数的顶部，而初始化仍旧在原来的地方。JavaScript引擎并没有重写代码：每次调用函数时，声明都会重新提升。

···

	var name = 'Jog'; //全局变量
	function prison() {
		console.log(a); //输出undefined
		var a = 1；//局部变量
		console.log(a); //输出1
		console.log(name); //输出Jog
	}
	prison();
···

```
	
	var name = 'Jog';
	function prison() {
		console.log(name); //输出undefined
		var name = 'Hans';
	}
	prison();
```
**此处name的声明被提升到函数的顶部，变量查找时先从局部作用域开始，未找到则由内而外最后到全局作用域。**

接下来我们详细分析一下JavaScript的提升方式。

###变量提升与执行环境
学习任何一门语言，都像学习魔术一样，初时引人迷惑，惊叹；然而当秘密被揭开时几乎令人失望，JavaScript不外如是。

* 1. 提升

执行某代码块时，JavaScript引擎先解释，再运行。解释过程主要几个过程：

	- (1) 声明该作用域内var变量
	- (2) 声明并初始化函数参数
	- (3) 声明并初始化声明式函数

*[详细可查看本系列笔记JavaScript之解释与执行机制](https://github.com/codingplayboy/javascript_notes/blob/master/js_compileRun.md)*

* 2. 执行环境与执行环境对象