# ECMAScript 6入门

ECMAScript 6，也叫ECMAScript 2015，简称ES6，是JavaScript的下一代标准，15年正式发布。2009年发布的改进版本ES5，引入了 Object.create()、Object.defineProperty()、getters
和 setters、严格模式以及 JSON 对象。相对于ES5的改进，ES6的变化是具有里程碑性的，它将改变我们编写JavaScript代码的方式。

## 块级作用域

在ES5中是没有块级作用域的，作用域都是以函数为划分的，定义的变量或函数都是在其上级函数作用域内作为局部变量或直接在全局作用域暴露作为全局变量（浏览器下即window的属性，Node环境下属于global对象）：

```

	// ES5
	var a = 1;
	function test() {
		console.log('external');
	}
	function start() {
		var a = 10;
		
		if (a > 1) {
			function test() {
				console.log('internal');
			}
		}
		test();
	}
	start(); // 
	console.log(a); // 1
```
对于以上例子，在ES5环境下执行，输出“internal”；在ES6环境运行则输出“external”。

## let和const

### let

ES6新增let命令，类似var是用来声明变量，不同的是：let声明的变量只存在let所在块级作用域内。

```

	var a = 1;
	let b = 1;
	if(true) {
		console.log(b); // error: b is not defined
		let b = 2;
		var a = 2;
		console.log(a); //2
		console.log(b); //2;
	}
	console.log(a); //2
	console.log(b); //1
```

如上代码，在let声明变量b之前，访问该变量会报错：变量未定义。

> let声明的变量仅在当前块级作用域存在，且只能在let声明之后访问该变量。

### const

