# JavaScript之闭包与高阶函数（二）
高阶函数，是指可以作为参数传递或者可以作为返回值输出的函数。在JavaScript中，函数是第一类对象，其既可以作为参数传递，也可以作为其他函数的返回值输出，显然是高阶函数。

## 高阶函数之参数传递
把函数当作参数传递，最常见的要数回掉函数，，我们常常在回掉函数中存放的是代码中易变的逻辑部分

### 回调函数
在JavaScript的学习和开发过程中，回调函数的使用率可谓尤其高，在DOM事件注册事件处理程序，ajax异步请求等等诸多场景频繁使用回调函数。

```

	/**
	 * [addHandler 处理事件注册兼容]
	 * @param {[type]} elem    [dom元素]
	 * @param {[type]} type    [事件类型]
	 * @param {[type]} handler [事件处理程序]
	 */
	function addHandler(elem, type, handler) {
		if (document.addEventListener) {
			elem.addEventListener(type, handler);
		}else if (document.attachEvent) {
			elem.attachEvent('on' + type, handler);
		}else {
			elem['on' + type] = handler;
		}
	}
	function fn1(e) {
		console.log(e);
	} 
	var div = document.getElementById('myDiv');
	addHandler(div, 'click', fn1);
```
又比如Array.prototype.sort方法，接受一个函数当作参数，函数里面封装的是对数组进行排序的规则。

```

	var arr = [1, 3, 5, 7, 2, 4, 6];
	arr.sort(function(a, b) {
		return a - b; //从小到大排序，如需从大到小排序，只需return b - a;
	});
```

## 高阶函数之返回值输出
相信大家对函数式编程一定不陌生，至少都耳熟过，，在函数式编程中，将函数当作返回值输出很是常见。

### 类型检测
JavaScript是一门动态的脚本语言，其数据类型直到程序运行时才能确定，于是在JavaScript中对数据类型进行检测判断不如Java等其他静态语言来的简便直接。

#### typeof和instanceof 
JavaScript中,常见的两个判断数据类型的方法是使用typeof和instanceof,在大多数情况下还是能正确判断出数据类型的，但这两者都有其局限性，因此，其使用也必然有局限性。

```

	console.log(typeof null); //object
	var arr = [];
	console.log(arr instanceof Array);//true
	function test() {
		console.log(arguments instanceof Array); //输出false
	}
	test();
```
只有当arr是数组，且与Array构造函数在同一全局作用域中，arr instanceof Array才返回true，如果arr是某一frame中定义的数组，上式返回false。

#### Object.prototype.toString
Object.prototype.toString方法总是返回字符串，如Object.prototype.toString.call([1,2,3])总是返回"[object Array]",Object.prototype.toString.call('str')总是返回"[object String]"，Object.prototype.toString.call(12)总是返回"[object Number]",所以可以使用Object.prototype.

*[更多关于calls与apply可查看本系列笔记篇JavaScript之this,call与apply](http://blog.codingplayboy.com/index.php/2016/01/09/js_executioncontext/)*

```
	
	var isType = function(type) {
		return function(obj) {
			return Object.prototype.toString.call(obj) == '[object ' + type + ']';
		}
	}
	var isString = isType('String');
	console.log(isString('testString')); //输出true
	var isArray = isType('Array');
	console.log(isArray([1,3,5])); //输出true
```

#### 作用域安全的构造函数
构造函数，即是一个函数，通过new操作符调用，以new操作符调用时，构造函数内部this指向新创建的构造函数实例对象。

```

	function Animal(name, type) {
		this.name = name;
		this.type = type;
	}
	var animal1 = new Animal('wangwang', 'Dog');
	console.log(animal1.name, animal1.type); //输出wnagwnag DOg
	var animal2 = Animal('miaomiao', 'Cat');
	console.log(window.name, window.type); //输出miaomiao Cat
	console.log(animal2.name, animal2.type); //TypeError: Cannot read property 'name' of undefined
```
当使用new操作符调用Animal构造函数时，会创建一个新的Animal对象，此对象拥有name和type属性；当直接调用Animal构造函数时，this对象指向全局环境即window(普通函数方式调用时，this总是指向全局环境，[更多关于this指向问题可查看本系列笔记篇JavaScript之this,call与apply](http://blog.codingplayboy.com/index.php/2016/01/09/js_executioncontext/)，于是上述代码window上有name和type属性，animal2上没有。

上述问题该如何解决呢？既然问题源自this,那就从this着手：

```

	function Animal(name, type) {
		if (this instanceof Animal) {
			this.name = name;
			this.type = type;
		}else {
			return new Animal(name, type);
		}
	}
	var animal1 = new Animal('wangwang', 'Dog');
	console.log(animal1.name, animal1.type); //输出wnagwnag DOg
	var animal2 = Animal('miaomiao', 'Cat');
	console.log(window.name); //输出undefined
	console.log(animal2.name, animal2.type); //输出miaomiao Cat
```
在使用new调用构造函数时，this指向Animal实例，this instanceof Animal返回true；当直接调用构造函数时，this指向全局环境window，this instanceof Animal返回false，此构造函数内部处理强制返回一个新实例，于是animal2中包含name和type属性。

## 函数绑定
函数绑定常指在调用某一函数时，将此函数内部this指向特定上下文环境，就是函数内部this和上下文环境的绑定。

```

	var Animal = {
		name: 'Cat',
		getName: function() {
			return this.name;
		}
	};
	var Animal2 = {
		name: 'Dog'
	};
	console.log(Animal.getName()); //输出Cat
	var div = document.getElementById('myDiv');
	div.addEventListener('mouseover', Animal.getName); //返回undefined
	div.addEventListener('click', function(e) {
		console.log(this);
		console.log(Animal.getName()); //输出Cat
	});
```
**如上代码中第一个输出Cat，很好理解，this指向Animal;而第二个返回undefined是为什么呢，因为在给DOM元素注册事件处理程序时，会修改事件处理程序的this指向，使其指向当前DOM元素(IE8除外，IE8中指向全局环境window)；第三处输出Cat是因为DOM事件修改的是事件处理程序的this指向，即此处Animal.getName()的包含环境中的this，此包含函数形成一个闭包，在此闭包中直接调用Animal.getName()，getName函数里面this指向Animal。**

我们再看一段代码：

```

	Function.prototype.bind = function(context) {
		var self = this;
		return function() {
			self.apply(context, arguments);
		};
	};
	var Animal = {
		name: 'Cat',
		getName: function() {
			//console.log(this.name);
			return this.name;
		}
	};
	var Animal2 = {
		name: 'Dog'
	};
	console.log(Animal.getName.bind(Animal2)()); //输出DOg
	var ani = Animal.getName.bind(Animal);
	console.log(ani(1,2,3)); //输出Cat,此时bind方法返回的函数内部的arguments属性就是一个包含1,2,3的类数组
	var div = document.getElementById('myDiv');
	div.addEventListener('click', Animal.getName.bind(Animal)); 
	//点击后返回Cat
```
我们在Function原型上定义了bind函数，其返回一个函数，这个函数形成一个闭包，其this与其包含函数this并不指向同一环境，于是使用了self保存包含函数的this对象，self便指向调用bind方法的函数，然后又使用apply调用此函数，传入了context上下文环境，及内部闭包函数的参数。

**注：在调用setTimeout,setInterval时，其内部this总是指向全局环境window，因此定时器函数中被调用的函数经常需要我们手动为其绑定上下文环境。**

```

	var obj = {
		test: function() {
			console.log(this); //输出object{test: function},即指向obj
			setTimeout(function() {
				console.log(this === window); //输出true
			},300);
		}
	}
	obj.test();
```

## 函数柯里化
### currying
函数柯里化(currying)又叫部分求值。一个currying的函数接收一些参数，接收了这些参数之后，该函数并不是立即求值，而是继续返回另一个函数，刚才传入的参数在函数形成的闭包中被保存起来，待到函数真正需要求值的时候，之前传入的所有参数都能用于求值。

```

	var totalCost = 0;
	var cost = function(money) {
		totalCost += money;
	};
	cost(100);
	cost(200);
	cost(400);
	cost(800);
	console.log(totalCost); //输出1500
```
如上累计计算totalCost值，每次都调用cost方法，假如我们只需保存前几次的增加值，在最后一次才求值，如何实现为好呢？可以用currying函数化思想简单实现：

```

	var cost = (function() {
		var args = [];
		return function() {
			if (!arguments.length) {
				var num = 0;
				for (var i = 0, l = args.length; i < l; i++) {
					num += args[i];
				}
				return num;
			}else {
				[].push.apply(args, arguments);
			}
		}
	})();
	cost(100);
	cost(200);
	cost(400);
	cost(800);
	console.log(cost()); //输出1500
```
上述代码每次调用cost传入参数时并未真正进行计算，而只是把参数保存在cost函数所形成的闭包中，在其返回函数中可随时访问进行操作，最后需要进行求值时只需要以无参数形式调用cost即可。

**接下来阐述如何实现一个通用的currying化函数：**

```

	var currying = function(fn) {

	};
	Function.prototype.currying = function() {
		var self = this;
		var args = []; //保存所有参数值
		return function() { //形成闭包，返回一个新函数，能保存对args的访问
			if (!arguments.length) {
				return self.apply(this, args); //调用currying化函数，并把args作为参数传入。
			}else {
				[].push.apply(args, arguments); //将每次传入的新参数存入args
				return arguments.callee;
			}
		}
	};
	var cost = (function() {
		var num = 0;
		return function() {
			num = 0; //确保每次调用cost()时重新计算所有已保存值值
			for (var i = 0, l = arguments.length; i < l; i++) {
				num += arguments[i]; //对所有参数求和
			}
			return num;
		}
	})();
	var cost = cost.currying();
	cost(100);
	cost(200);
	cost(400);
	console.log(cost()); //输出700
	cost(800); 
	console.log(cost()); //输出1500
```
如上实现了一个currying函数，调用cost时明确传入了参数，则此时只是保存参数值，不进行业务计算，cost返回另一个函数。在不传参数调用cost时，对之前所有保存的参数值进行业务计算。

*注：currying化函数后调用的cost都是currying函数cost，而非原本的cost函数。*

### uncurrying
在JavaScript学习和开发过程中，我们经常需要让某对象去调用一个原本不属于自己的方法，如Array.prototype.push或是其他自定义对象上的方法，一般我们经常使用的是apply与call,[更多有关apply与call请访问本系列篇JavaScript之this,call与apply.](http://blog.codingplayboy.com/index.php/2016/01/09/js_executioncontext/)

```

	var obj = {
		name: 'Dog',
		getName: function() {
			return this.name;
		}
	};
	var obj2 = {
		name: 'Cat'
	};
	console.log(obj.getName.call(obj2)); //输出Cat
```
在本节，我们要使用uncurrying来实现通用的方法以实现需求：

```

	Function.prototype.uncurrying = function() {
		var self = this;
		return function() {
			var obj = Array.prototype.shift.call(arguments);
			return self.apply(obj, arguments);
		};
	};
	var push = Array.prototype.push.uncurrying();
	(function() {
		push(arguments, 7);
		console.log(arguments); //输出[1,3,5,7]
	})(1,3,5);
```
uncurrying使Array.prototype.push方法变成了一个通用的push函数，push即等价于Array.prototype.push.call。
在调用Array.prototype.push.uncurrying()时，self为Array.prototype.push,调用push(arguments, 7)时，obj为[1,3,5]，uncurrying方法返回函数内部的arguments为后文自执行函数调用push传入的7，self.apply(obj, arguments)即等价于Array.prototype.push.apply([1,3,5], 7)。

## 函数节流
JavaScript中，有某些函数是可能会被频繁调用，从而造成性能问题，甚至出现功能上的偏差。如：
- window.onresize事件，在为window注册了resize事件后，若频繁的修改窗口大小，就会频繁的触发resize事件，会非常消耗性能，容易造成浏览器卡顿甚至崩溃。
- mouseenter，mouseleave，mousemove等事件，这些事件由鼠标划入，划出，移动触发，其触发频率可能非常快，特别是当我们在这是事件处理程序中定义了动画处理时，问题尤其需要注意，我们知道，每一段动画一般都有一个执行周期，多个动画都是压入一个动画队列，一次执行，假如我们触发事件的频率非常快，动画将重复触发。

```

	<!doctype html>
	<html>
	<head>
		<meta charset="utf-8">
		<title>无标题文档</title>
		<script src="./js/jquery.min.js">
		</script>
	</head>
	<body>
		<div class="a1" style="width:100px; height:100px; background-color:#000;"></div>
		<div class="a2" style="width:1000px; height:200px; background-color:#F00; display:none;"></div>
		<div class="b1" style="width:100px; height:100px; background-color:#000;margin-top:20px;"></div>
		<div class="b2" style="width:1000px; height:200px; background-color:#F00; display:none;"></div>
		<script type="text/javascript">
			$('.a1').mouseenter(function(){
				$('.a2').fadeIn();
			});
			$('.a1').mouseleave(function(){	
				$('.a2').fadeOut();
			});
			$('.b1').mouseenter(function(){
				$('.b2').fadeIn();
			});
			var timeoutId = null;
			$('.b1').mouseleave(function(){
				clearTimeout(timeoutId);
				timeoutId = setTimeout(function(){
					$('.b2').fadeOut();
				}, 500);
				
			});
		</script>
	</body>
	</html>
```
![代码效果图](http://blog-resource.bj.bcebos.com/photos/2016_01/timeout.png)
[代码演示地址](http://jhssdemo.duapp.com/demo/smallcase/htc/html_mouseEvent.html)
如上，当我们快速多次划过第一个小黑块时，其动画会重复执行，在我们的鼠标动作停止后，还在进行动画；而对于第二个小黑块，则不会发生这种现象。因为在第二个黑块的mouseleave事件中设置了定时器，并且在每次动画之前都清除之前的定时器，保证了最后只会有一个定时器，即一次动画被执行。

接下来，我们实现一个通用的节流函数：

```
	
	var throttle = function(fn, context, interval) {
		clearTimeout(fn.tId);
		fn.tId = setTimeout(function() {
			fn.apply(context || null);
		}, interval || 500);
	};
```
将之前第一个黑块的mouseleave事件修改成如下即可实现第二个黑块的函数节流效果：

```

	var aFn = function() {
		$('.a2').fadeOut();
	};
	$('.a1').mouseleave(function(){	
		throttle(aFn);
	});
```

## 分时函数
上节中，讲述了函数节流方式用以解决某些函数被频繁调用的问题，然而有些函数也会被重复调用，但这些函数执行次数却不能少，如何在不减少其执行次数的前提下，把其对页面性能的破坏降到最低呢？函数节流显然不能解决这个问题，聪明的程序员提出了分时函数这一解决方法。

现在企业内部都很流行webIM，企业即时通讯，在打开页面时，需要加载员工列表，这个列表常常会是成百上千的，一个好友用一个div节点显式的话，那就意味着需要创建成百上千个节点并添加到页面document，在短时间添加如此多节点显然是会影响浏览器页面性能的，如下：

```

	var arr = [];
	for (var i = 1; i < 1000; i ++) {
		arr.push(i);
	}
	var renderStaff = function(data) {
		for (var i = 0, l = data.length; i < l; i++) {
			var div = document.createElement('div');
			div.innerHTML = i;
			document.body.appendChild(div);
		}
	};
	renderStaff(arr);
```
解决问题的方法之一就是使用分时函数，如下b把1000个节点分批进行：

```
	
	var timeChunk = function(arr, fn, count) {
		var obj, t;
		var len = arr.length;
		var start = function() {
			for (var i = 0; i < Math.min(count || 1, arr.length); i ++) {
				var obj = arr.shift();
				fn(obj);
			}
		};

		return function() {
			t = setInterval(function() {
				if (!arr.length) {
					return clearInterval(t);
				}
				start();
			}, 200);
		};
	};
```
于是之前加载1000节点的代码重构如下，分批创建节点，每隔200ms创建8个节点timeChunk接收三个参数，第一个为节点数据，第二个为创建节点业务逻辑，第三个为每批所创建节点的个数：

```

	var arr = [];
	for (var i = 1; i < 1000; i ++) {
		arr.push(i);
	}
	var renderStaff = timeChunk(arr, function(data) {
		var div = document.createElement('div');
		div.innerHTML = i;
		document.body.appendChild(div);
	}, 8);
	renderStaff(arr);
```


## 惰性加载函数
由于浏览器间的行为差异，在JavaScript的代码中，为了达到不同浏览器的兼容，经常需要写入大量判断语句，对于执行不同代码块，比如要实现一个在诸浏览器间通用的事件绑定函数addHandler，可以参考如下代码实现：

```

	var addHandler = function(elem, type, handler) {
		if (window.addEventListener) {
			return elem.addEventListener(type, handler);
		}else if (window.attachEvent) {
			return elem.attachEvent('on' + type, handler);
		}else {
			return elem['on' + type] = handler;
		}
	}
```
如上代码，在每次执行时都需要重新做条件判断，我们要如何才能做到在每个环境下只做一次判断呢？addHandler依然声明为一个普通函数，在第一次进入条件分支时，函数内部会重写这个函数，重写的就是符合当前环境的addHandler函数，当再次进入addHandler函数时，函数里不再存在条件分支语句：

```

	var addHandler = function(elem, type, handler) {
		if (window.addEventListener) {
			addHandler = function(elem, type, handler) {
				elem.addEventListener(type, handler); 
			}
		}else if (window.attachEvent) {
			addHandler = function(elem, type, handler) {
				elem.attachEvent('on' + type, handler);
			}
		}else {
			addHandler = function(elem, type, handler) {
				elem['on' + type] = handler;
			}
		}
		addHandler(elem, type, handler);
	};
	//测试一下
	var div = document;
	addHandler(div, 'click', function() {
		console.log('add handler 1');
	});
	addHandler(div, 'click', function() {
		console.log('add handler 2');
	});
```