# JavaScript代码风格

---
JavaScript编程代码风格指导。

## 命名约定

------

- 命名具有描述性

```

	// bad
	function q() {
	  // ...stuff...
	}

	// good
	function query() {
	  // ..stuff..
	}
```

- 驼峰式命名对象、函数、实例

```

	// bad
	var OBJEcttsssss = {};
	var this_is_my_object = {};
	var o = {};
	function c() {}

	// good
	var thisIsMyObject = {};
	function thisIsMyFunction() {}
```

- 构造函数首字母大写

```

	// bad
	function user(options) {
	    this.name = options.name;
	}

	var bad = new user({
	    name: 'nope'
	});

	// good
	function User(options) {
	    this.name = options.name;
	}

	var good = new User({
	    name: 'yup'
	});
```

- 定义私有属性时以下横线_开头

```

	// bad
	this.__firstName__ = 'Panda';
	this.firstName_ = 'Panda';

	// good
	this._firstName = 'Panda';
```

- 使用self保存this引用

```

	//bad
	function () {
	    var _this = this;
	    return function () {
	    	console.log(_this);
	  	};
	}

	//good
	function () {
	    var self = this;
	    return function () {
	    	console.log(self);
	    };
	}
```

- 总是尽可能命名函数

```

	// bad
	var log = function (msg) {
	    console.log(msg);
	};

	// good
	var log = function log(msg) {
	    console.log(msg);
	};
```

## 变量

------

- 永远使用var关键字声明变量

```

	// bad
	superPower = new SuperPower();

	// good
	var superPower = new SuperPower();
```

- 对每个变量单独使用var声明

```

	// bad
	var items = getItems(),
    	goSportsTeam = true,
    	dragonball = 'z';

	// good
	var items = getItems();
	var goSportsTeam = true;
	var dragonball = 'z';
```

- 未赋值变量最后声明

```

	// bad
	var i, len, dragonball,
    	items = getItems(),
    	goSportsTeam = true;

	// bad
	var i;
	var items = getItems();
	var dragonball;
	var goSportsTeam = true;
	var len;

	// good
	var items = getItems();
	var goSportsTeam = true;
	var dragonball;
	var length;
	var i;
```

- 在作用域顶部声明变量

```

	// bad
	function () {
  		test();
  		console.log('doing stuff..');

  		//..other stuff..

  		var name = getName();

  		if (name === 'test') {
    			return false;
  		}

  		return name;
	}

	// good
	function () {
  		var name = getName();

  		test();
  		console.log('doing stuff..');

  		//..other stuff..

  		if (name === 'test') {
    			return false;
  		}

  		return name;
	}

	// bad - unnecessary function call
	function () {
  		var name = getName();

  		if (!arguments.length) {
    			return false;
  		}

  		this.setFirstName(name);

  		return true;
	}

	// good
	function () {
  		var name;

  		if (!arguments.length) {
    			return false;
  		}

  		name = getName();
  		this.setFirstName(name);

  		return true;
	}
```


## 对象

------


- 使用对象字面量方式创建对象


```
		
	//Bad
	var person = new Object();

	//Good
	var person = {};
```

- 不使用保留字作为对象属性

```

	//Bad
	var person = {
		name: 'style',
		private: true
	};

	//Good
	var person = {
		name: 'style',
		self: true
	};
```

- 使用可读性的同义词替代保留字

```

	//Bad
	var person = {
		class: 'guide'
	};

	//Bad
	var person = {
		klass: 'guide'
	};

	//Good
	var person = {
		type: 'guide'
	};
```
## 属性

------

- 用点号访问对象属性

```

	var luke = {
  		jedi: true,
  		age: 28
	};

	// bad
	var isJedi = luke['jedi'];

	// good
	var isJedi = luke.jedi;
```

- 通过变量访问对象属性时使用中括号

```

	var luke = {
  		jedi: true,
  		age: 28
	};

	function getProp(prop) {
  		return luke[prop];
	}

	var isJedi = getProp('jedi');
```

## 数组

------

- 使用字面量创建数组

```

	//Bad
	var a = new Array();

	//Good
	var a = [];
```

- 使用push方法添加数组项

```
	
	var a = [];

	//Bad
	a[a.length] = 'style guide';

	//Good
	a.push('style guide');
```

- 使用slice方法复制数组

```

	var a = [1,3,5,7,9];
	var copyA = [];
	var len = a.length;
	var i;
	
	//Bad
	for (i = 0; i < len; i++) {
		copyA[i] = a[i];
	}
	
	//Good
	copyA = a.slice();
	
```

- 使用slice方法将类数组对象转换成数组

```

	function trigger() {
  		var args = 			
		Array.prototype.slice.call(arguments);
	}
```

## 字符串

---

- 字符串使用单引号

```

	// bad
	var name = "Bob Parr";

	// good
	var name = 'Bob Parr';

	// bad
	var fullName = "Bob " + 		this.lastName;

	// good
	var fullName = 'Bob ' + this.lastName;
```

- 长字符串应该通过字符串连接多行书写

```

	// bad
	var errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';

	// bad
	var errorMessage = 'This is a super long error that was thrown because \
	of Batman. When you stop to think about how Batman had anything to do \
	with this, you would get nowhere \
	fast.';

	// good
	var errorMessage = 'This is a super long error that was thrown because ' +
  	'of Batman. When you stop to think about how Batman had anything to do ' +
  	'with this, you would get nowhere fast.';
```

- 当以编程方式创建字符串时，应该使用join方法而不是字符串连接

```

	var items;
	var messages;
	var length;
	var i;

	messages = [{
  		state: 'success',
  		message: 'This one worked.'
	}, {
  		state: 'success',
  		message: 'This one worked as well.'
	}, {
  		state: 'error',
  		message: 'This one did not work.'
	}];

	length = messages.length;

	// bad
	function inbox(messages) {
  		items = '<ul>';

  		for (i = 0; i < length; i++) {
    		items += '<li>' + messages[i].message + '</li>';
  		}

  		return items + '</ul>';
	}

	// good
	function inbox(messages) {
  		items = [];

  		for (i = 0; i < length; i++) {
    		// use direct assignment in this case because we're micro-optimizing.
    		items[i] = '<li>' + messages[i].message + '</li>';
  		}

  		return '<ul>' + items.join('') + '</ul>';
	}
```

## 函数

------

- 永远不要在非函数块中使用函数声明，而可以使用命名函数表达式

```

	// bad
	if (currentUser) {
  		function test() {
    		console.log('Nope.');
  		}
	}

	// good
	var test;
	if (currentUser) {
  		test = function test() {
    		console.log('Yup.');
  		};
	}
```

- 永远不要将参数命名为arguments

```

	// bad
	function nope(name, options, arguments) {
  		// ...stuff...
	}

	// good
	function yup(name, options, args) {
  		// ...stuff...
	}
```

## 代码块

------

- 所有的多行代码块使用花括号

```

	// bad
	if (test)
  		return false;

	// good
	if (test) return false;

	// good
	if (test) {
  		return false;
	}

	// bad
	function () { return false; }

	// good
	function () {
  		return false;
	}
```

## 比较符号

------

- 优先使用===和！==，最后使用==和！=

- 使用快捷方式判断

```

	// bad
	if (name !== '') {
  		// ...stuff...
	}

	// good
	if (name) {
  		// ...stuff...
	}

	// bad
	if (collection.length > 0) {
  		// ...stuff...
	}

	// good
	if (collection.length) {
  		// ...stuff...
	}	
```

## 注释

------

- 多行注释使用/** ... */

```

	// bad
	// make() returns a new element
	// based on the passed in tag name
	//
	// @param {String} tag
	// @return {Element} element
	function make(tag) {
	  // ...stuff...
	  return element;
	}

	// good
	/**
	 * make() returns a new element
	 * based on the passed in tag name
	 *
	 * @param {String} tag
	 * @return {Element} element
	 */
	function make(tag) {
	  // ...stuff...
	  return element;
	}
```

- 单行注释使用//双右斜杠，且注释单独成行，置于代码块上方

```

	// bad
	var active = true;  // is current tab

	// good
	// is current tab
	var active = true;

	// bad
	function getType() {
	    console.log('fetching type...');
	    // set the default type to 'no type'
	    var type = this._type || 'no type';

	    return type;
	}

	// good
	function getType() {
	    console.log('fetching type...');

	    // set the default type to 'no type'
	    var type = this._type || 'no type';

	    return type;
	}
```

- 使用//FIXME:声明问题

```

	function Calculator() {

    	// FIXME: shouldn't use a global here
    	total = 0;

    	return this;
	}
```

- 使用//TODO:声明所需实现

```

	function Calculator() {

    	// TODO: total should be configurable by an options param
  		this.total = 0;

  		return this;
	}
```

## 空格

------

- 设置tab键为2个或4个空格

- 在花括号前加一个空格

```

	// bad
	function test(){
	    console.log('test');
	}

	// good
	function test() {
	    console.log('test');
	}

	// bad
	dog.set('attr',{
	    age: '1 year',
	    breed: 'Bernese Mountain Dog'
	});

	// good
	dog.set('attr', {
	    age: '1 year',
	    breed: 'Bernese Mountain Dog'
	});
```

- 在包含条件表达式的括号前加一个空格

```

	// bad
	if(isJedi) {
	    fight ();
	}

	// good
	if (isJedi) {
	    fight();
	}
```

- 包含函数参数列表的括号前不加空格

```

	// bad
	function fight () {
		console.log ('Swooosh!');
	}

	// good
	function fight() {
	    console.log('Swooosh!');
	}
```

-运算符前后加一个空格

```

	// bad
	var x=y+5;

	// good
	var x = y + 5;
```

- 在文件末尾加单一分行符

```

	// bad
	(function (global) {
	  // ...stuff...
	})(this);


		// bad
	(function (global) {
	  // ...stuff...
	})(this);↵

	// good
	(function (global) {
	  // ...stuff...
	})(this);↵
	↵
```

- 在代码块和下一代码语句之间留一空行

```

	// bad
	if (foo) {
	    return bar;
	}
	return baz;

	// good
	if (foo) {
	    return bar;
	}

	return baz;

	// bad
	var obj = {
	    foo: function () {
	    },
	    bar: function () {
	    }
	};
	return obj;

	// good
	var obj = {
	    foo: function () {
	    },

	    bar: function () {
	    }
	};

	return obj;
```

## 分号

------

- 自执行函数前后分号

```

	// bad
	(function () {
	    var name = 'Skywalker'
	    return name
	})()

	// good
	(function () {
	    var name = 'Skywalker';
	    return name;
	})();

	// good (guards against the function becoming an argument when two files with IIFEs are concatenated)
	;(function () {
	    var name = 'Skywalker';
	    return name;
	})();
```

- 每条代码语句后加分号

```

	//bad
	var name = 'style'

	//good
	var name = 'style';
```

## 强制转换

------

- 类型转换放在代码语句前部

```

	//  => this.reviewScore = 9;

	// bad
	var totalScore = this.reviewScore + '';

	// good
	var totalScore = '' + this.reviewScore;

	// bad
	var totalScore = '' + this.reviewScore + ' total score';

	// good
	var totalScore = this.reviewScore + ' total score';
```

- 使用parseInt数值转换时，总是声明转换进制

```

	var inputValue = '4';

	// bad
	var val = new Number(inputValue);

	// bad
	var val = +inputValue;

	// bad
	var val = inputValue >> 0;

	// bad
	var val = parseInt(inputValue);

	// good
	var val = Number(inputValue);

	// good
	var val = parseInt(inputValue, 10);
```

- boolean值转换

```

	var age = 0;

	// bad
	var hasAge = new Boolean(age);

	// good
	var hasAge = Boolean(age);

	// good
	var hasAge = !!age;
```


## 存取器

------

- 使用存取器方法存取值

```

	// bad
	dragon.age();

	// good
	dragon.getAge();

	// bad
	dragon.age(25);

	// good
	dragon.setAge(25);
```

- 返回boolean值的方法使用isVal()或hasVal()形式

```

	// bad
	if (!dragon.age()) {
	    return false;
	}

	// good
	if (!dragon.hasAge()) {
	    return false;
	}
```

## 构造函数

------

- 不要重写构造函数原型

```

	function Jedi() {
	    console.log('new jedi');
	}

	// bad
	Jedi.prototype = {
	    fight: function fight() {
	        console.log('fighting');
	    },

	    block: function block() {
	        console.log('blocking');
	    }
	};

	// good
	Jedi.prototype.fight = function fight() {
	    console.log('fighting');
	};

	Jedi.prototype.block = function block() {
	    console.log('blocking');
	};
```

## jQuery

------

- jQuery对象变量加前缀$

```

	// bad
	var sidebar = $('.sidebar');

	// good
	var $sidebar = $('.sidebar');
```

- 混存DOM查询

```

	// bad
	function setSidebar() {
	    $('.sidebar').hide();
			// ...stuff...
	    $('.sidebar').css({
	    	'background-color': 'pink'
	    });
	}

	// good
	function setSidebar() {
	    var $sidebar = $('.sidebar');
	    $sidebar.hide();

	    // ...stuff...

	    $sidebar.css({
	      'background-color': 'pink'
	    });
	}
```


























