# 设计模式
设计模式是在开发过程不断地总结出的对于某类场景的通用解决方案或思路。

## 单例模式
保证仅有一个实例，并提供全局访问。

```

	function Single(name) {
		this.name=  name;
		this.instance = null;
	}
	Single.init = function(name){
		if (!this.instance) {
			this.instance = new Single(name);
		}
		return this.instance;
	}
	var a = Single.init("惊鸿");
```

```

	var Single = (function () {
		var instance = null;
		function Single(name) {
			if (!instance) {
				this.name = name;
				instance = this;
			}
			return instance;
		}
		return Single;
	})();
	var a = new Single("惊鸿");
	var b = new Single("君彦");
	console.log(a === b);
```
违反单一职责原则，创建对象和管理实例逻辑在一起。

- 惰性单例

	在需要的时候才创建对象实例。
	
	```
	
		var  single = function(fn) {
			var instance = null;
			return function() {
				return result || (result = fn .apply(this, arguments));
			};
		};
		function createObj(name) {
			return {
				name: name
			};
		}
		single(createObj)("惊鸿");
	```
	
## 策略模式

定义一系列算法，将他们封装，使其可以相互替换。

```

	var strategy = function(level) {
		if (level === 'top') {
			console.log('great');
		}
		if (level === 'middle') {
			console.log('good');
		}
	};
```
	
> 策略模式的目的是将算法的使用与实现分离开来。策略模式一般把程序分为两部分：策略类，封装具体的算法实现；环境类，接收请求，将请求委托给某一策略类。

```

	//策略
	var strategy = {
		"top": function(name) {
			console.log("great " + name);
		},
		"middle": function(name) {
			console.log("good " + name);
		}
	};
	
	//环境
	var exec = function(level, name) {
		return strategy[level](name);
	};
	exec('top', '惊鸿'); //great 惊鸿 
	exec('middle', '君彦'); //good 君彦
```

## 代理模式

代理和本地都显式地实现同一个接口。

图片预加载：
```

	var myImage = (function() {
		var imgNode = document.createElement('img');
		document.body.appendChild(imgNode);
		return {
			setSrc: function(src) {
				imgNode.src = src;
			}
		}
	})();
	var proxyImage = (function() {
		var img = new Image;
		img.onload = function() {
			myImage.setSrc(this.src);	
		};
		return {
			setSrc: function(src) {
				myImage.setSrc('file:///C:/Users/loading.gif');
				img.src = src;
			}
		}
	})();
	proxyImage.setSrc('http://imgcache.qq.com/music/photo/k/000GGDys0yA0NK.jpg');
```
没有改变myImage的接口，通过代理对象，给系统添加新行为；给img节点设置src和图片预加载两个功能隔离在两个对象，互不影响。
单一职责原则，一个接口应该仅有一个引起它变化的原因。
开发-封闭原则，对调用开放，对修改封闭。


缓存代理：
```

	var mult = function() {
		var res = 1;
		for (var i = 0, l = arguments.length; i < l; i++) {
			res = res * arguments[i];
		}
		return res;
	};
	var plus = function() {
		var res = 0;
		for (var i = 0, l = arguments.length; i < l; i++) {
			res = res + arguments[i];
		}
		return res;
	};
	
	var proxyFactory = function(fn) {
		var cache = {};
		return function() {
			var args = Array.prototype.join.call(arguments, ',');
			if (args in cache) {
				return cache[args];
			}
			return cache[args] = fn.apply(this, arguments);
		};
	};
	
	var proxyMult = proxyFactory(mult);
	var proxyPlus = proxyFactory(plus);
```
还可以缓存ajax异步请求数据

## 享元模式
享元模式分离内部状态和外部状态；
对象池

## 发布-订阅模式
var Event = {
	listen: fn,
	trigger: fn
}

## 适配器模式
添加适配接口

## 装饰者模式

## 状态模式
允许对象在内部状态改变时改变其行为