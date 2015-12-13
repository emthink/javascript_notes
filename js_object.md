---
layout: post
title: JavaScript Notes
permalink: readme.md
description: javascript object oriented
date: 2015-12-12 16:02:30 +08:00
tags: "JavaScript 笔记分享"
---
#JavaScript之面向对象
传统面向对象语言有一个通用标志，即类的概念，通过类可以创建任意多个具有相同属性和方法的对象。而JavaScript中没有类的概念，它的对象与传统面向对象语言有所不同。
##动态类型与静态类型
何谓动态，何谓静态？编程语言按数据类型大体分为两类：动态类型语言，静态类型语言。
> 静态类型语言在编译时便已确定变量的类型，而动态类型语言的变量类型要到程序运行时，待变量被赋值后，才会具有某种类型。

典型的静态类型语言有Java, C#等，而在JavaScript中，当我们为某变量赋值时，并不需要判断其类型，其显然是一门典型的动态类型语言。

##JavaScript对象和原型链
###对象
与对象最紧密相关当然是变量了
JavaScript的中有五种简单数据类型（也称基本数据类型）以及一种复杂数据类型。
简单数据类型包括数字, 字符串, 布尔值（true和false）, null, undefined值。
复杂数据类型即Object。**JavaScript中除了简单数据类型值，其他所有值均为对象。**
####JavaScript变量之基本类型值与引用类型值
JavaScript变量可能包含两种不同数据类型的值：基本数据类型值和引用类型值。将一个值赋给变量时，解析器会判断这个值的类型。
> 基本类型值指的是简单的数据段，是按值访问的，可以操作保存在变量中的实际值；而引用类型值指那些可能由多个值构成的对象，是引用类型的一个实例，是按引用访问的。

- **基本类型** 即包括如前所述的五种。
- **引用类型** 一种数据结构，用以描述对象的属性和方法。

*注：引用类型值和引用类型不同。*

JavaScript引用类型主要包括Object，Array，Date，RegExp，Function以及为了操作基本类型值提供的三个特殊引用类型：Boolean，Number和String。**可以说，JavaScript中对象即是某种引用类型的实例，我们使用的大多数引用类型值又都是Object类型的实例。**
###JavaScript创建对象
不同于许多传统面向对象语言使用基于类的对象，JavaScript使用基于原型的对象。
- **对象字面量** 一个对象字面量就是在一对花括号中的零或多个名值对。
- **Object构造函数** 调用Object构造函数，可使用new Object()形式创建对象。*Object.create()方式创建对象亦很常见。*

###JavaScript继承

继承（Object Orient）向来是面向对象语言最突出特征之一，如Java等传统OO语言都支持基于类的继承，而JavaScript中是没有类的概念，其主要依靠原型链实现继承。

> 基于原型链的委托机制就是原型继承的本质，基本思想是利用原型让一个引用类型继承另一个引用类型的属性和方法。

####原型
对象的原型，就JavaScript真正实现而言，其实对象并无原型，而是对象的构造器（构造函数）与原型。所谓‘对象把请求委托给自己的原型’即是把请求委托给其构造器原型。JavaScript对象的\_\_proto\_\_属性默认即指向其构造器原型对象。

```
	var a = new Object();
	console.log(a.__proto__ === Object.prototype);  //true
```

####构造函数，原型与实例
**每一个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，而构造函数的实例都包含一个指向原型对象的内部指针。JavaScript中所有对象都默认继承自根对象Object。**

给一个构造函数的原型对象赋值为另一个类型的实例，此时原型对象即包含指向另一原型对象的指针，而另一个原型对象中也包含指向其相应构造函数的指针，如此便构成了原型与实例的关系链，也即原型链。依此可实现不同对象间的属性和方法的继承。

- **默认原型** 所有引用类型默认都继承了Object，所有函数的默认原型都是Object实例，而如前所述：构造函数的实例都包含一个指向原型对象的内部指针，因此默认原型都包含一个指向Object.prototype（即Object原型）的内部指针。

```
	function Animal() {
		this.property = 'Animal';
	}
	Animal.prototype.getAnimalProperty = function() {
		return this.property;
	}
	function Dog() {  
		this.dogproperty = 'Dog';
	}
	Dog.prototype = new Animal();//继承自Animal
	Dog.prottotype.getDogProperty = function() {
		return this. dogproperty; 
	}
	var dog = new Dog();
	alert(dog.getDogProperty);
	alert(dog.getAnimalProperty);
```

##总结
- **对象的根对象** JavaScript对象都源于（继承自）根对象Object。
- **对象源自克隆** 定义对象，不是通过实例化类，而是把另一对象作为原型并克隆之。
- **对象记忆性** 请求可以在原型链中依此向后传递，每个对象都会记住自己的原型，详细请回顾上文关于对象原型之说的\_\_proto\_\_属性。
-  **对象请求传递性** 若当前对象无法响应当前请求，便将其委托给其构造器原型。




