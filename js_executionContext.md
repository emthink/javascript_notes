---
layout: post
title: Execution Context
permalink: js_executionContext.md
description: introduce this, call, applay
date: 2016-01-02 10:17:12 +08:00
tags: "Share JavaScript Notes"
---

#JavaScript之this、call与apply

在JavaScript学习中，this的重要性不明而寓，有必要对其深入学习，而与this息息相关的有两个特别需要注意的就是call，apply，本篇就着手从this开始阐述，而后介绍call、apply之用法。

##this
**JavaScript中this总是指向一个对象，这个对象基于函数运行时的执行环境动态绑定，而非函数声明时所处环境。**

*[关于执行环境可查看本系列篇JavaScript之作用域与作用域链](https://github.com/codingplayboy/javascript_notes/blob/master/js_scope.md)*

###this指向
关于this指向问题，与函数调用息息相关，其可大致分为四类情况：

* (1). 方法调用； 即作为某对象的方法调用。
* (2). 函数调用；即作为普通函数调用。
* (3). 构造器函数调用；即以new方式调用。
* (4). call与apply调用；即以Function.prototype.call或Function.prototype.apply调用。

# 

* **1.方法调用**

```

	var obj = {
		name: 'Job',
		getName: function() {
			console.log(this === obj); //输出true
			console.log(this.name);  //输出Job
			return this.name;
		}
	};
	obj.getName();
```
**当函数作为对象的方法被调用时，this总是指向该对象。**

* **2.函数调用**

```

	var name = 'Job';
	var getName = function() {
		console.log(this === window);  //输出true
		console.log(this.name);  //输出Job
		return this.name;
	};
	getName(); //普通函数调用
```
**非严格模式下，当函数作为普通函数方式调用时，不论该函数调用时所处执行环境为何，this总是指向全局对象，JavaScript中即是window对象。**

再看如下实例，加深理解：
```

	<script>
		var id = 'windowId';
		document.getElementById('myDiv').onclick = function() {
			console.log(this.id); //输出myDiv
			var fn = function() {
				console.log(this.id); //输出windowId
			};
			fn(); //普通函数调用
		};
	</script>
```

* **3.构造器函数调用**

```

	var Animal = function(name) {
		this.name = name;
	};
	var animal = new Animal('Dog');
	console.log(animal.name); //输出Dog
```
**当用new调用构造器函数时，总会返回一个对象，该对象可以看成是构造器函数一个实例，一般，此时构造器里的this就指向返回的这个对象。**
但还需要注意的是当构造器显式地返回一个对象时：
```

	var Animal = function(name) {
		this.name = name;
		return {
			name: 'Job'
		};
	};
	var animal = new Animal('Dog');
	console.log(animal.name); //输出Job	
```
此时，this指向的是构造器显式返回的对象。

* **4.call与apply调用**

```

	var obj = {
		name: 'Job',
		getName: function() {
			console.log(this === obj); 
			console.log(this.name);  
			return this.name;
		}
	};
	var obj2 = {
		name: 'Web'
	};
	console.log(obj.getName());  //输出Job
	console.log(obj.getName.call(obj2)); //输出Web
	console.log(obj.getName.apply(obj2)); //输出Web
```
当以Function.prototype.call或Function.prototype.apply方式调用函数时，动态传入参数改变执行环境，此时this指向传入的对象。
