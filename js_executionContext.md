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

> 调用一个函数时代码执行控制权就转移到此函数，同时将参数传入此函数，除了声明函数时定义的形式参数，每个函数都会接受两个附加参数：this和arguments。

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

##call与apply
call与apply是Function原型上定义的两个方法，其应用广泛。

* **1.call与apply的区别**

两者作用完全一样，不同之处在于传入参数的形式。
> call传入参数数量不固定，第一个参数代表所调用函数体内this的指向，而后依次为所调用函数所需参数。
> 
> apply接受最多两个参数，第一个参数指定被调用函数内this的指向，第二个参数为一个带下标的集合，可以是数组或者类数组，作为参数传给被调用函数。

```

	var fn = function(a, b, b) {
		console.log([a, b, c]);
	};
	fn.call(null, 1,2,3);
	fn.apply(null, [1,2,3]);
```
**需要注意的是，非严格模式下，使用call或apply时，第一个参数是必须的，当第一个参数传入null时，函数体内this就会指向被调用函数的宿主对象(其包含对象)；而严格模式下，this指向则为null**

* **2.call与apply的应用**

(1). 改变this指向
使用call或apply调用函数可改变函数体内this的指向。实例如前文中this指向相关实例。

(2). Function.prototype.bind
Function.prototype.bind可以用来绑定函数体内this指向，其参数为this的指向对象。IE8及以下不支持，这里我们可以自己实现一个。
```

	Function.prototype.bind = function(context) {
		var self = this;  //保存原函数的引用
		return function() { //返回一个新函数
			return self.apply(context, arguments); //将传入的参数指定为新函数体内this的指向对象
		};
	};	
	var obj = {
		name: 'Job'
	};
	var fn = function() {
		console.log(this.name);
	};
	fn.bind(obj)();
```

(3) 借用其他对象的方法
最熟悉的就是借用构造函数实现类似继承的效果：
```

	var Animal = function(name) {
		this.name = name;
	};
	var Dog = function() {
		Animal.apply(this, arguments);
	};
	Dog.prototype.getName = function() {
		return this.name;
	};
	
	var dog = new Dog('Dog');
	console.log(dog.getName());  //输出Dog
```
另一很常见的情况就是借用Array.prototype对象上的方法处理函数参数类数组arguments:
```

	(function() {
		Array.prototype.shift.call(arguments); //arguments为类数组[1,2,3]
		var args = Array.prototype.slice.apply(arguments); //将arguments类数组剩余参数转换为数组赋给args变量
		console.log(arguments); //输出[2, 3]
		console.log(args); //输出[2, 3]
	})(1,2,3);
```
上面借用Array.prototype上的shift方法移除参数列表的首位，当然也可以借用其他方法达到其他目的，如push, slice等。

JavaScript中，call与apply的应用是非常广泛的，在各类Js框架库中，都不难发现其身影。