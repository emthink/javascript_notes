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


```

	var name = 'Jog'; //全局变量
	function prison() {
		console.log(a); //输出undefined
		var a = 1；//局部变量
		console.log(a); //输出1
		console.log(name); //输出Jog
	}
	prison();
```

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

###变量提升与执行环境对象
学习任何一门语言，都像学习魔术一样，初时引人迷惑，惊叹；然而当秘密被揭开时几乎令人失望，JavaScript不外如是。

* 1. 提升

执行某代码块时，JavaScript引擎先解释，再运行。解释过程主要几个过程：

	- (1) 声明该作用域内var变量
	- (2) 声明并初始化函数参数
	- (3) 声明并初始化声明式函数

*[详细可查看本系列笔记JavaScript之解释与执行机制](https://github.com/codingplayboy/javascript_notes/blob/master/js_compileRun.md)*

* 2. 执行环境与执行环境对象

**执行环境（execution context）**是一种概念，每当函数被调用都会产生一个新的执行环境。

> **执行环境**定义了变量或函数有权访问的其他数据，决定了它们各自的行为。

	- 1. 每个函数都有自己的执行环境。  当执行流进入一个函数时，执行环境就被推入一个环境栈中；函数执行之后，栈将其执行环境弹出，控制权返回到之前的执行环境。
	- 2. 如果变量在当前执行环境内可访问，则该变量在当前作用域内。
	- 3. JavaScript访问变量，其实就是访问该执行环境对象（变量对象）中的属性。
	- 4. 全局执行环境是最外围的一个执行环境。
	
> **执行环境对象**--每个执行环境都有一个与之对应的**变量对象**，执行环境中定义的所有变量和函数都保存在这个对象中。

```

	function fn(arg) {
		var name = 'Far';
		inner();
		function inner() {
			console.log('inner');
		}
	}
	fn('test');
```
在调用fn时，其过程如下

	- 1. 创建一个空执行环境对象；
	- 2. 声明参数并赋值；{arg: 1}
	- 3. 声明局部变量；{arg:1, name: undefined}
	- 4. 预定义声明式函数；{arg:1, name: undefined， inner: function(){console.log('inner');}}
	- 5. 代码执行时，局部变量被赋值；{...name: 'Far'...}
	- 6. 执行环境对象上变量和函数属性保持不变，调用inner函数时，其内部会创建一个新的执行环境对象，依此可递归形成一条作用域链。

##作用域与作用域链

> 当一个变量在某执行回家内可以被访问，我们称该变量在当前**作用域**内。

代码某一执行环境中执行时，会创建该**执行环境对应的变量对象**的一个作用域链。

> JavaScript引擎在执行环境对象中查找作用域内的变量或函数，其查找顺序由内而外向上直到全局执行环境对象，这个顺序就形成**作用域链**。

作用域链的**前端**，始终是当前执行环境对应的变量对象。若此执行环境是函数，则将其活动对象作为变量对象。作用域链中的**下一个**变量对象来自于当前变量对象的包含（外部）执行环境，如此一直到全局执行环境；全局执行环境的变量对象始终是作用域链中的**最后**一个变量对象。

```

	var age = 22;
	var country = 'China';
	var name = 'Java';
	var job = 'Web';
	function outer() {
		console.log(age);  //输出22
		console.log(country); //输出undefined
		var country = 'Union';
		var name = 'Python';
		inner();
		function inner() {
			console.log(name); //输出Python
			console.log(job); //输出Web
		}
	}
	outer();
```
代码输出结果如上：

	- 1. outer函数执行时，首先在outer执行环境对象中查找age和country变量结果country存在但并未初始化赋值，输出undefined;而age未找到于是沿着作用域链向上到全局执行环境，在其变量对象中存在age属性，于是输出其值22.
	- 2. inner函数执行时创建自己的执行环境对象，其并没有定义name和job等变量，于是沿着作用域链向上到达outer函数的执行环境，在其变量对象中存在name于是输出其值Python；而未找到job于是继续向上直到全局执行环境，找到并输出其值，结束；若依然未找到，则会报错，停止运行。

**注：函数参数亦被当作变量对待，故其访问规则与普通变量相同。**

###延长作用域链

某些语句可以在作用域链的前端临时增加一个变量对象，该变量对象会在代码执行结束后移除。常见如：

* try-catch语句；
 catch语句会创建一个新变量对象，包含被抛出的错误对象的声明。
* with语句；
 with语句会创建一个包含语句接收对象的所有属性和方法的变量对象。

```

	function getAttr(data) {
		var obj = data;
		with(obj) {
			var o = location;
		}
		console.log(o);
	}
	getAttr(window);
```
上面with语句接收window对象，其创建的变量对象就包含了window对象所有属性和方法，于是可以在其执行环节直接访问location变量，也就是正常的window.location。

*强烈建议不要使用with语句。*

本篇笔记阐述JavaScript执行环境与执行环境对象，变量对象，作用域与作用域链，耗时四小时，还有诸多不足之处待日后补充改进。