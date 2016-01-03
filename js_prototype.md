---
layout: post
title: JavaScript Prototype
permalink: js_prototype.md
description: introduce javascript prototype
date: 2016-01-03 20:08:49 +08:00
tags: "Share JavaScript Notes"
---

#JavaScript之原型与原型链

在基于类的语言中，对象是类的实例，并且类可以从另一个类继承，如Java；JavaScript则是一门基于原型的语言，以原型链实现继承，其对象可以直接继承自另一对象，此篇详细阐述JavaScript之原型与原型链。

##原型
Javascript中创建的每个函数都有一个prototype属性，这个属性是一个指针，指向一个对象，这个对象的作用即是包含可以由特定类型实例共享的属性和方法，这个对象就是函数的原型对象。

默认情况，所有的原型对象都会有一个constructor属性，这个属性包含一个指向prototype属性所在函数的指针。

调用构造函数创建一个新实例后，实例的内部将包含一个指向构造函数原型对象的指针，在ECMA-262中定义此指针为[[Prototype]]，并不能被显式的访问到，而在Firefox,Safari和Chrome中每个对象上有一个\_\_proto\_\_属性。

*\_\_proto\_\_显示的是实例与构造函数原型对象间的关系，而非实例与构造函数间的关系。*

```

	function Animal(name) {
		this.name = name;
	}
	Animal.prototype.age = 3;
	Animal.prototype.getName = function() {
		return this.name;
	};
	var animal = new Animal('Dog');
	console.log(animal.getName());  //输出Dog
```
以上代码中，Animal为构造函数，Animal.prototype指向构造函数原型对象；原型对象中constructor属性指向构造函数，即Animal.prototype.constructor指向Animal；在构造函数实例中，其\_\_proto\_\_属性指向构造函数原型对象。

* 1. 原型与实例属性访问

```
	
	function Animal(name) {
		this.name = name;
	}
	Animal.prototype.age = 3;
	Animal.prototype.getName = function() {
		return this.name;
	};
	var animal1 = new Animal('Dog');
	var animal2 = new Animal('Cat');
	console.log(animal1.age); //输出3--原型属性
	console.log(animal2.age); //输出3--原型属性
	animal2.age = 4;
	console.log(animal2.age); //输出4--实例属性
	delete animal2.age;
	console.log(animal2.age); //输出3--原型属性
```
> 获取某对象属性时，首先从该对象实例本身开始，若该实例中找到该属性，则返回该属性值；若未找到，则继续查找该实例对象指向的构造函数原型对象，若找到则返回值。

(1) hasOwnProperty()方法
hasOwnProperty()方法可以检测一个属性是在原型上还是实例上，只有当给定属性为对象实例属性时返回true：

```

	function Animal(name) {
		this.name = name;
	}
	Animal.prototype.age = 3;
	Animal.prototype.getName = function() {
		return this.name;
	};
	var animal1 = new Animal('Dog');
	var animal2 = new Animal('Cat');
	console.log(animal1.hasOwnProperty('name'));  //true
	console.log(animal1.hasOwnProperty('age'));  //false
	animal1.age = 4;
	console.log(animal1.hasOwnProperty('age'));  //true
```

(2) 原型与in
单独使用in操作符时，只要通过对象能访问到给定属性即返回true，无论属性是在实例还是原型上定义：

```

	function Animal(name) {
		this.name = name;
	}
	Animal.prototype.age = 3;
	Animal.prototype.getName = function() {
		return this.name;
	};
	var animal1 = new Animal('Dog');
	var animal2 = new Animal('Cat');
	console.log(animal1.hasOwnProperty('name'));  //true
	console.log('name' in animal1); //true
	console.log(animal1.hasOwnProperty('age'));  //false
	console.log('age' in animal1); //true
	console.log('eat' in animal1); //false
```

* 2.实例与原型的引用关系

实例在创建时，其内部指针[[Prototype]](在上文提到的\_\_proto\_\_)指向构造函数原型对象，存在引用关系。

```

	function Animal(name) {
		this.name = name;
	}
	var animal = new Animal('Dog');
	Animal.prototype.age = 3;
	Animal.prototype.getName = function() {
		return this.name;
	};
	animal.getName(); //输出Dog
```
可以看到虽然实例早于原型中getName方法创建，但其依然可以调用该方法，因为实例通过引用指向原型对象，原型对象变化自然能被实例访问到。然而，对于如下这种情况：
```

	function Animal(name) {
		this.name = name;
	}
	var animal = new Animal('Dog');
	Animal.prototype = {
		constructor: Animal,  //设置constructor值为Animal，确保constructor属性返回适当值，详细见上文关于constructor属性说明
		age: 3,
		getName: function() {
			return this.name;
		}
	};
	var animal2 = new Animal('Cat');
	console.log(animal2.getName());  //输出Cat
	animal.getName();  //TypeError: undefined is not a function
```
此处，首先创建了一个实例，随后重写了构造函数原型对象，再在实例上调用getName方法时报错；而重写构造函数原型对象之后创建的实例调用getName方法可以正常返回对应值。这是因为重写原型对象之后，之前创建的实例引用的依然是之前的原型对象，其与现有原型之间并无联系，而之后创建的实例[[Prototype]]指针引用的就是现有原型。

* 3.原型对象存在问题

```

	function Animal(name) {
		this.name = name;
	}
	Animal.prototype.age = 3;
	Animal.prototype.partner = ['one'];
	Animal.prototype.getName = function() {
		return this.name;
	};
	var animal1 = new Animal('Dog');
	var animal2 = new Animal('Cat');
	console.log(animal1.partner); //输出["one"] 
	console.log(animal2.partner); //输出["one"] 
	animal1.partner.push('two');
	console.log(animal1.partner); //输出["one", "two"] 
	console.log(animal2.partner); //输出["one", "two"] 
	console.log(animal1.partner === animal2.partner); //输出true
```
可以看到原型中所有属性都被实例共享，特别是对于引用类型的属性，如上的partner属性。

* 4.默认原型

所有引用类型默认都继承自Object，所有构造函数默认原型都是Object的实例，默认原型都会包含一个内部指针，指向Object.prototype。

* 5.构造函数，原型与实例的关系

每个构造函数都有一个原型对象，由prototype属性指向；原型对象包含一个指向构造函数的指针constructor；而实例都包含一个指向构造函数原型对象的内部指针[[Prototype]]。

##原型链

每一个构造函数都有一个原型对象，当我们让某一原型对象等于另一构造函数的实例，此时该原型对象就包含一个指针，该指针指向这一构造函数的原型对象，该指针指向的原型对象中包含一个指向这一构造函数的指针，同样我们可以令该指针指向的原型对象等于另一构造函数的实例，如此递进，则形成一条实例与原型的链条，即原型链。

```

	function Parent() {
		this.name = 'parent';
	};
	Parent.prototype.getName = function() {
		return this.name;
	};
	function Child() {
		this.childname = 'child';
	}
	Child.prototype = new Parent();
	Child.prototype.getChildName = function() {
		return this.childname;
	};
	var child = new Child();
	console.log(child.getName()); //输出parent
	console.log(child.getChildName());  //输出child
```
如上代码，child实例指向Child原型，Child原型等于Parent实例，即指向Parent原型。可见本质即是以一个新类型（构造函数）的实例重写原型对象，形成原型链。

原型与原型链是JavaScript实现继承的基础，下一篇详细介绍JavaScript之继承。
