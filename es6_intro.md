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

```

    // ES5
    for (var i = 0; i < 10; i++) {
        setTimeout(function() {
            console.log(i);
        },50);
    }
    
    //ES6
    for (let i = 0; i < 10; i++) {
        setTimeout(function() {
            console.log(i);
        },50);
    }
```

如上代码，第一个所有的输出都是10，因为所有的setTimeout函数都是引用同一个i变量，所以值都为10；而使用let声明变量后，每一次循环声明的i都只在for块级作用域内，所以分别输出0至9。

> let声明的变量仅在当前块级作用域存在，且只能在let声明之后访问该变量，重复定义同一变量会抛出语法错误（SyntaxError）。

### const

ES6新增const命令用来声明常量，必须且只能在声明时赋值，不可随意修改，否则会抛出语法错误（SyntaxError）。

## Symbol

ES5中，JavaScript共有六种原始类型（primitive type）:

- Object
- String
- Number
- Boolean
- Undefined
- Null

ES6新增了第七种原始类型：Symbol：

```

    var mySymbol = Symbol();
    Symbol(description);
    Symbol('test') === Symbol('test'); // false
```

如上，直接调用Symbol(description)，该对象接收一个可选的描述符，创建一个新的symbol，它既不是字符串也不是数字或对象，它是一种全新的类型，不等于任何其他值，包括其他任何Symbol,因此它是唯一的，可以作为键名，可以避免命名冲突：

```

    var mySymbol = Symbol();
    var obj = {};
    obj[mySymbol] = 'Test';
```

### 全局symbol注册表

直接调用Symbol()创建的symbol并不能在全局代码共享，有时候我们希望能有一些全局共享的唯一symbol，这时，我们需要使用Symbol.for()和Symbol.forKey()方法：

```

    Symbol.for('Test');
```

- for(key) 根据给定的描述符值在全局查找并返回已有的symbol，若不存在，则使用该值创建一个新symbol。
- keyFor(symbol) 全局查找给定symbol的描述符

### 属性

- length Symbol长度，值为0
- prototype Symbol构造函数的原型

### Symbol.iterator

除了我们自定义的一些symbols，ES6还新增了一些内置symbols，其中Symbol.iterator是和迭代器相关：

	
> Symbol.iterator方法返回某一对象默认迭代器,和for...of一起使用访问可遍历数据结构。

### Symbol与for...in遍历

在ES5，我们经常使用for...in结构遍历数组或对象，for...in可以遍历数组或者对象的所有可枚举属性。前文提到，ES6新增的Symbol类型也是可以做对象属性键名的，这一点与字符串一样，但symbols并不能被for...in循环遍历到：

```

    var obj = {
		[Symbol("test")]: 'test',
		name: 'jh',
		age: 23
	};

	for (var i in obj) {
   		console.log(i); // 输出 "name" 和 "age"
	}
```

如上，symbol在for...in循环不能遍历，Object.getOwnPropertyNames()方法自然也无法返回symbol类型属性，但是新增的Object.getOwnPropertySymbols()方法就可以得到我们需要的结果。

## 迭代器和for...of

在ES5我们习惯性使用for...in遍历数组或对象，但是其有很多问题：

- 会遍历数组自定义可枚举属性，如（arr.name）
- 遍历顺序是随机的
- 不能遍历到symbol类型的属性

ES6新增了for...of遍历结构可以弥补这些缺陷。

### for...of

for...of可以遍历集合，数组，字符串，类数组，NodeList类型的数据，但不能遍历object对象:

```

	let str = "test";

	for (let value of str) {
  		console.log(value);
	}
	// 输出 "t" "e" "s" "t"
	
	let arr = [2, 4, 6];

	for (let value of arr) {
  		console.log(value);
	}
	// 输出 2 4 6
```

### 迭代器

拥有\[Symbol.iterator]()的对象就是可迭代的对象，包括数组，对象，集合，类数组，NodeList等等类型数据。可以通过迭代器对所有可迭代对象进行迭代访问，以for...of为例：

```

	var someString = "hi";
	
	// for...of遍历
	for (var value of someString) {
	    console.log(value)
	}
	// 输出 "h" "i"
	
	// 直接使用迭代器对象遍历
	var iterator = someString[Symbol.iterator]();
	iterator + "";      // "[object String Iterator]"
 
	iterator.next();    // { value: "h", done: false }
	iterator.next();    // { value: "i", done: false }
	iterator.next();	// { value: undefined, done: true }
```
如上，我们定义了一个字符串，然后通过其Symbol.iterator属性获取遍历器方法，执行该方法返回一个迭代器对象，赋值给iterator，然后通过调用迭代器对象的next()方法循环遍历该字符串，对于其他类型的数据，也是同理进行遍历，遍历过程总结如下：

- for...of循环，首先调用该遍历数据的的\[Symbol.iterator]()方法，然后返回一个新的迭代器对象。
- 迭代器对象可以是任意具有next()方法的对象
- for-of循环将重复调用next()方法，每次循环调用一次，直到遍历结束

## 集合

JavaScript中现有的Object对象，以键值对形式存储数据，我们可以很方便获取，添加，删除对象属性；但是其键名总是字符串类型，而且我们需要避免与内置方法同名，同时无法直接获取对象键值对的长度。针对这些问题，ES6新增了几种集合类型：Set,Map以及WeakSet,WeakMap。

### Set

Set是由一群互不相同的值组成的不能够通过索引访问到的可变集合。

#### 创建

> Set可以从任何可遍历数据中提取元素，构造出一个新的集合。

```
	
    var setOfCity = new Set(['BeiJing', 'ShangHai']);
```

形式如上new Set(pairs);pairs参数可以是任何可以遍历的数据结构。

#### 方法和属性

Set集合提供很多有用的属性和方法：

- size 长度属性
- add(value) 增加集合元素
- delete(value) 删除集合元素
- clear() 清空集合
- has 判断集合是否包含某元素，效率比数组indexOf快的多
- forEach(func) 类似Array的forEach方法，接受一个回调函数，依此对集合每一项执行该函数，该函数接收参数如（value, value, set）。
- set.keys()、set.values()和set.entries()返回各种迭代器，是为了兼容Map而提供的


### Map

Map是由若干键值对组成的集合，和Set相似。

#### 创建

```

    var mapOfCity = new Map([[0, 'BeiJing'], [1, 'ShangHai']]);
```

形式如上new Map(pairs);pairs参数可以是一个已有的Map对象，可以是一个由二元数组组成的数组，也可以是一个迭代生成二元数组的生成器。

#### 方法和属性

- size 长度属性
- has(key) 判断集合中是否存在某键名
- get(key) 返回一个键名对应的值，若不存在则返回undefined
- set(key, value) 新增或修改键值对
- delete(key) 根据键名删除特定键值对
- clear() 清空Map
- forEach(func) 类似Array的forEach方法，接受一个回调函数，依此对集合每一项执行该函数，该函数接收参数如（value, key, map）。
- keys() 遍历返回所有键名
- values() 遍历返回所有值
- entries() 遍历返回所有集合项

### 弱集合WeakSet和WeakMap

Set和Map集合为其每个键或值都保持了强引用，比如DOM元素，即使被移除了，但其引用依然存在，其占用内存无法被回收，容易发生内存泄漏，于是有了WeakSet和WeakMap，与对应的Set和Map创建和使用方式相同，但有以下几点不同：

- WeakSet的值和WeakMap的键类型必须是对象
- WeakSet的值和WeakMap不可遍历
- WeakSet只提供has、add和delete方法
- WeakMap只提供has、get、set 和delete方法

#### 内存泄漏

ES6新增WeakSet和WeakMap以避免上文提到的内存泄漏：一个弱集合并不对其中的对象保持强引用。当弱集合中的一个对象被回收时，它将被从集合中移除；如果一个键仍被引用，其对应的值也将仍被引用。


## 模板字符串

ES6新增了一种新型的字符串字面量语法--模板字符串（template strings）：

```

    var name = 'jh';
    var templateHtml = `My Name is ${name}`;
    $(".wrap").html(`
        <h1>Template String</h1>
        <p>这是目标字符串，可以多行书写，作者：
        ${name}。</p>
    `);

```

如上，${name}被称为模板占位符，占位符里面可以是变量或表达式，变量值或表达式返回值将被输出到字符串中占位符所在位置。而且目标字符串可以换行书写，同时模板字符串中所有的空格、新行、缩进，都会原样输出在生成的字符串中。

**注：在模板字符串中出现反字符或$或{字符时，需要转义才能使用。**

模板字符串是很方便，但其尚不可能替代模板引擎的地位，在其中并未实现诸如循环，条件判断等语法。

## 箭头函数

ES6新增了一种编写函数的语法--箭头函数，语法非常简单，形如：标识符=>表达式。

```

    var fn = a => ++a;
    var fn = (a, b) => a + b;
    var fn = (a, b) => {
        console.log(a, b);
        return a + b;
    };
```

如上，对于单个参数的函数，可以直接书写，而多个参数，则需要包含在括号内；对于单个表达式的函数体，会自动将表达式值作为函数返回值，因此不需要return语句，而包含在花括号块里面的函数体不会自动返回，我们必须明确return语句，否则返回值将为undefined。

**注：ES6中，紧随箭头后的{被解析为代码块的开始，而非对象字面量。诸如a => {}这类代码会被解析为返回undefined的空箭头函数，所以单独的对象字面量需要用小括号包裹。**

### this

头函数没有它自己的this值，箭头函数内的this值继承自外围作用域，而我们知道当函数作为对象方法调用时，其内部可能通过this引用调用者对象的其他属性，所以需要考虑两种情况：

- 作为对象属性方法调用的函数使用非箭头函数定义，这些函数this与调用者对象相关
- 其它情况全都使用箭头函数。

```

    //ES 5
    var a = [1,2,3];
    var b = {
        arr: [],
        add: function(a) {
            this.arr.push(a)
        }
    };
    
    a.forEach(function(i, v) {
        b.add(v);
    });

    // ES6
    var a = [1,2,3];
    var b = {
        arr: [],
        add(a) {
            this.arr.push(a)
        }
    };
 
    a.forEach((i, v) => b.add(v));
```

## 不定参数和默认参数

我们在编写功能函数或API时，很多时候函数接受的参数数量和值都是不固定的，而且我们通常会为参数指定默认值或者未指定默认值，JavaScript会为其默认指定为undefined。

```

    function say(name, word) {
        word = word || 'hi';
        console.log(word + ', ' + name);
    }
    say('xjg'); // hi, xjg
    function reduceAdd() {
        var res = 0;
        for (var i = 0; i < arguments.length; i++) {
            res += arguments[i];
        }
        return res;
    }
    reduceAdd(2,9,12); //23
```

### 不定参数

如上代码，reduceAdd函数实现了累加所有参数的功能，函数接收的参数值和数量均不固定，我们用到了arguments类数组变量，它包含了传递给函数的所有参数，在ES6中，我们可以以如下方式实现累加功能：

```

    var reduceAdd = (start, ... arr) => {
        var res = start;
        for (var value of arr) {
            res += value;
        }
        return res;
    };
    reduceAdd(2,9,12); //23
```

如上，所有函数参数中，只有最后一个才可以被标记为不定参数。函数被调用时，不定参数前的所有参数都正常一一对应，其他所有参数放在一个数组中赋值给不定参数，若没有其他参数，则不定参数为空数组。

### 默认参数

如前文的say()函数接收一个word参数,一个name参数，我们为其word设置了默认值'hi',若参数有值，则覆盖默认值，name参数默认值为undefined，在ES6中可以实现如下：

```

    var say = (name, word='hi') => console.log(word + ', ' + name);
    say('xjg'); // hi, xjg
```

再看如下加法函数：

```

    var add = (a=2, b=a) => a + b;
    add(); //4
```

我们发现：函数默认值表达式在函数调用时是自左向右求值，后面的参数默认表达式可以使用该参数之前已经设置好的参数值。


## 代理Proxy

S6规范定义了一个全新的全局构造函数：代理（Proxy）。接收两个参数：目标对象（target）与句柄对象（handler）。
> 代理的行为很简单：根据句柄对象的方法覆写代理的内部方法，然后将代理的所有内部方法转发至目标对象。

内部方法，共有14种：get/set hasProperty getPrototypeOf call constructor等。

``` 

    var target = {};
    var handler = {
        set: function (target, key, value, receiver) {
            throw new Error("请不要为这个对象设置属性。");
        }
    };

    var proxy = new Proxy(target, handler);
    proxy.name = "angelina";
    // Error: 请不要为这个对象设置属性。
```

如上，定义了一个handler.set()方法来覆盖代理的对应内部方法，然后可以拦截所有给对象属性赋值的行为；同理，其他所有14种句柄方法也是一样可以被覆盖，且都是可选的，没被句柄拦截的内部方法会直接指向目标，调用后直接触发。


## 类class与子类subclassing

严格来说，ES5中，并没有类和子类的概念

### class

在ES5中，我们创建构造函数，是直接使用function关键字，然后约定俗成将函数名首字母大写：

```

    function Animal() {}
```

ES6中则使用类来创建对象构造函数，如前文代码首先是class关键字声明开始创建类，在类对象中提供一个constructor属性指定该类唯一的构造函数，于是该方法也被称为方法构造函数（method constructor）。

```

    class Animal {
        constructor(type) {
            this.radius = radius;
            Animal.nums++;
        };
        static eat(food) {

        };
        static get nums() {
            return !this._nums ? 0: this._nums;

        };
        static set nums(val) {
            this._nums = val;

        };
        say(words) {
            console.log('I can ay' + words);

        };
    }
    
```

如上。在给命名构造函数（类）添加方法的同时也给函数的.prototype属性对象添加了相应的方法，于是通过这个类构造的实例也包含相应的方法；当然，我们也需要能区分特殊函数和普通函数，这里提供了static关键字，带static关键字则表明是类方法，不带static关键字则是实例方法。

**注：在class类对象中，方法间通常以分号为分隔符，当然分隔符也是可选的。**

### extends

和其他面向对象一样，ES6也给JavaScript提供了子类与父类建立连接的方式L：通过extends关键字：

```

    class Dog extends Animal {
    }
```

extends后面可以是任何拥有prototype属性的构造函数。

### 继承

在面向对象语言中，继承是最基本的一个特性，JavaScript作为一门面向对象语言，

在JavaScript中，一个最典型的使用构造函数和原型组合实现继承的方式如下：

```

    function Animal(type) {
        this.type = type;
        Animal.nums++;

    }

    Animal.eat = function eat(food) {

    };

    Object.defineProperty(Animal, 'nums', {
        get: function() {
            return !this._nums ? 0: this._nums;
        },
        set: function(val) {
            this._nums = val;
        }

    });
    Animal.prototype.say = function(words) {
        console.log('I can ay' + words);
    };

    function Dog(type, name) {
        Animal.call(this, type);
        this.name = name;
    }

    Dog.prototype = new Animal();
    Dog.prototype.constructor = Dog;
```    

而在ES6，则提供了更简洁的实现方式：

```

    class Animal {
        constructor(type) {
            this.radius = radius;
            Animal.nums++;
        };
        static eat(food) {

        };
        static get nums() {
            return !this._nums ? 0: this._nums;

        };
        static set nums(val) {
            this._nums = val;

        };
        say(words) {
            console.log('I can ay' + words);

        };
    }

    class Dog extends Animal {
        constructor(type, name) {
            super(type);
            this.name = name;
            this.id = super.nums * (Math.random() * 10000).toString(36);
        };
    }
```

### super

通过class定义父类，子类，然后使用extends使子类继承父类，有时我们会在子类定义父类中的同名方法或属性，这样会覆盖继承的方法或属性，但有时我们仍然需要访问到父类的该方法或属性，这时我们可以使用另一个关键字：super：

```

    class Dog extends Animal {
        constructor(type, name) {
            super(type);
            this.name = name;
            this.id = super.nums * (Math.random() * 10000).toString(36);
        };
    }
```

如上的super.nums就是访问的父类的nums属性。

### new.target

很多时候，我们会有多个子类继承同一个父类，子类继承父类时，由父类分配构造函数this对象，指定其构造函数类型，但是若存在多个子类，父类无法判断应该指定何种类型，这里我们就需要用到new.target元属性，该属性指明构造函数；new方式创建子类实例时，在子类构造函数内调用super时，会传递new.target的值，this对象类型便确定了：

```

    class Animal() {
        constructor() {
            return new.target;
        };
    }

    class Dog extends Animal {
        constructor() {super();}
    }

    class Cat extends Animal {
        constructor() {super();}
    }
    
    new Animal(); // Animal
    new Dog(); // Dog
    new Cat(); // Cat
```

## 模块module

ES6中石使用模块的目的是：通过多个文件和目录来组织项目,所有代码都可以按需彼此访问并高效加载。

- 每一个ES6模块都是一个包含JS代码的文件
- 在ES6模块中，代码总是在严格模式下运行
- ES6模块提供了import和export关键字。
- 所有的声明都被限定在模块的作用域中，外部不可见，需要将模块公共API暴露给其他模块使用。

 
### export

### import

导入其他模块关键字：

```
    
    import * as cows from "cows";
```

### export 

导出模块关键字：

```
    function fn() {}
    function fn2() {}
    exports {fn, fn2};
```

### 聚合模块

我们也可以将从其他模块导入的模块，在本模块导出：

```

    // 导入"module1"的fn,fn2，并将其导出
    export {fn, fn2} from "module1";

    // 导入"module2"并将它导出的内容全部导出
    export * from "module2";
```

### 模块运行

JS引擎运行一个模块分四个步骤：

- 1.语法解析：解析源代码，检查语法错误
- 2.加载：递归加载所有导入的模块
- 3.链接：对每一个加载的模块，创建作用域并将模块内声明的或从其他模块导入的变量或函数绑定到作用域
- 4.运行新加载的模块。

 




    
    






    

