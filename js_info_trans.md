---
layout: post
title: New Post
permalink: new-page.html
description: Some Description
date: 2016-01-29 11:41:59 +08:00
tags: "some tags here"
---

# 原型继承

在大部分语言中，存在类和对象。类继承自其他类。
在JavaScript中，继承则是基于原型的。这意味着在JavaScript里面没有类的概念，其对象继承自其他对象。

## 继承之\_\_proto\_\_属性
假如一个rabbit对象继承自另外的animal对象，在JavaScript里这意味着其特殊属性rabbit.\_\_proto\_\_ = animal.

![\_\_proto\_\_](http://javascript.info/files/tutorial/intro/object/prototype.png)

假如一个rabbit属性可访问，并且解释器不能在rabbit对象里查找到它，它循着\_\_proto\_\_指针在animal对象里查找。

这个例子使用\_\_proto\_\_只在Chrome和FireFox浏览器起效，这样完全是为了简便，稍后我们会进行跨浏览器处理。

```

	var animal = {eats: true};
	var rabbit = {jumps: true};
	rabbit.__proto__ = animal; //继承
	alert(rabbit.eats); //true
```
eats属性确实来自于animal对象，如下图：

![http://javascript.info/files/tutorial/intro/object/proto4.png](http://javascript.info/files/tutorial/intro/object/proto4.png)

如果在rabbit对象上查找到该属性，则不检查\_\_proto\_\_属性。

例如，当子对象中存在eats属性，它的父级将被忽略：

```

	var animal = {eats: true};
	var fedUpRabbit = {eats: false};
	fedUpRabbit.__proto__ = animal;
	alert(fedUpRabbit.eats); //false
```
![http://javascript.info/files/tutorial/intro/object/fedup.png](http://javascript.info/files/tutorial/intro/object/fedup.png)

我们可以在animal对象里设置一个方法随后可以在rabbit对象上访问。

```

	var animal = {
	    eat: function() {
	        alert("I'm full");
	        this.full = true;
	    }
	};
	var rabbit = {
		jump: function() {
			//do something
		}
 	};
 	rabbit.__proto__ = animal;
 	rabbit.eat();
```