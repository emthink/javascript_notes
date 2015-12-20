#JavaScript之闭包与高阶函数（一）

JavaScript虽是一门面向对象的编程语言，但同时也有许多函数式编程的特性，如Lambda表达式，闭包，高阶函数等。

> 函数式编程是种编程范式，它将电脑运算视为函数的计算。函数编程语言最重要的基础是 λ 演算（lambda calculus）。而且λ演算的函数可以接受函数当作输入（参数）和输出（返回值

##闭包

何谓闭包？对于闭包众位各有己见，今我试说之，**闭包，常指有权访问其外部作用域中变量和参数的函数。**最常见的就是在某函数内部创建另一个函数。如：

```

    var count = (function() {
        var item = 0;
        
        return {
            add: function(num) {
                item += typeof num === 'number' ? num : 1;
            },
            value: function() {
                return item;
            }
        }
    })();
```
*此处把函数返回的结果赋值给count，该函数返回一个包含两个方法的对象，对象中的方法均可访问其包含函数中的变量及参数。count中保存的是该对象的一个引用，对象中的方法依然可以访问自执行函数中的变量，而且访问的是变量本身。*

> 闭包 函数可以访问它创建时所处的上下文环境中的变量以及参数，this以及arguments除外。

闭包其实并不是很好阐述，与我而言，自我理解与向他人阐述差别甚大，但也要试着去征服它。闭包的形成与变量息息相关，尤其是变量的作用以及变量生命周期，请看细说：

###闭包与变量

**闭包中所保存的是整个变量对象--执行环境（上下文环境）中的一个表示变量的对象的引用，访问执行环境中变量即是访问该变量对象中的变量。**

> 变量对象 每个执行环境（上下文环境）中的一个表示所有变量的对象，全局环境的变量对象始终存在，而局部环境的变量对象只在其执行过程中存在。

典型案例如下：
```

    function myNumber() {
        var count = [];
        for (var i = 0; i < 10; i ++) {
            count[i] = function() {
                return i;
            }
        }
        return count;
    }
```
这个函数会返回一个函数数组，这个数组会不会乖乖返回自己的数字呢？当然不会，事实上，每个函数都返回10。为什么呢？细细道来，因为每个函数的作用域链中都保存着myNumber()函数的活动对象（变量对象），他们都引用同一个变量对象，当然也引用同一个变量i,当myNumber()函数返回后i为10。

再看如下代码：
```

    function myNumber() {
        var count = [];
        for (var i = 0; i < 10; i ++) {
            count[i] = （function(num) {
                return function() {
                    return num;
                };
            })(i);
        }
        return count;
    }
```
在此将自执行匿名函数结果赋值给数组，调用每个匿名函数时，传入变量i，而函数参数是**按值传递**，即将变量值复制给参数num，在此匿名函数内部又创建并返回了一个访问num参数的闭包，count数组中的函数均保存有自己的num变量的副本，于是，便返回各自的值了。

####变量的作用域

变量分全局变量与局部变量，在函数中声明变量时，以var关键字定义的变量即是局部变量，而不带var关键字的就变成全局变量。
```
    
    var c = 3
    var func = function() {
        var a = 1;
        b = 2;
        alert(b);//2
        alert(c);//3
    }
    func();
    alert(b);//2
    alert(a);//Uncaught ReferenceError: b is not defined
```
我们知道，**在函数中查找变量时，首先在当前函数执行环境作用域查找，若未找到，则随当前执行环境创建的作用域链往外层查找，直到全局对象为止，这里的查找是从内向外查找的**

####变量的生命周期

上面说到变量作用域，这里谈谈变量生命周期：

- **全局变量**，其生命周期在整个程序运行时间内永久存在，除非主动销毁，否则可以随时调用。
- **局部变量**， 其在所属作用域代码执行过程中存在，当运行完成，且不存在外部调用此上下文环境中的变量时，即被销毁，否则依然存在。

```

    var func = function() {
        var res = [1,2,3,4,5,6];
        var a = 0;
        return function() {
            alert(res[a]);
            a++;
        }
    };
    var f = func();
    
    func()();//1
    func()();//1
    
    f();//1
    f();//2
    f();//3
```
试比较执行fun()()与f()的弹出值，是不一样的，貌似在f()中a一直存在。当执行var f = func();时，f函数返回的是一个匿名函数的引用，此匿名函数可以访问func()被调用时的上下文环境（执行环境），局部变量即在其中，局部变量所处环境能被外界访问，局部变量就不会被销毁。

###闭包的作用

1. **封装变量**

闭包可以封装形成‘私有变量‘，如:实现计算乘积:
```

    var mult = function() {
        var a = 1;
        for (var i = 0, len = arguments.length; i < len; i++) {
            a = a * arguments[i];
        }
        return a;
    }
    alert(mult(1,2,3,4));
```

2. **模仿块级作用域**

JavaScript中是没有块级作用域的概念的，如：
```

    function block() {
        var res = [1,3,5,7,9];
        for (var i = 0; i < res.length; i++) {
            alert(res[i]);
        }
        var i;//重新声明变量
        alert(i);//5
    }
```
如上代码所见，i变量定义后在整个包含函数中均可访问。**JavaScript中for语句并不会形成块级作用域，其整个作用域是包含函数创建的，而且对变量的后续声明都将被忽略。**
要达到块级作用域效果，我们可以形成闭包来模仿之，如：
```

    (function() {
        //块级作用域
    })()
```
3. **添加私有变量或函数**

通过在私有作用域定义私有变量或函数，可以形成私有成员，如：
```

    (function() {
        var name = 'xjg';
        function getName() {
            reutn name;
        }
        Person = function(val) {
            name = val;
        }
        Person.getName = function() {
            return name;
        }
    })();
    var p1 = new Person('Anagle');
    alert(p1.getName());//Anagle
    alert(getName())//ReferenceError: getName is not defined
```
此处，name就变成了一个静态私有变量。

###闭包与内存泄漏

局部变量本来在函数退出时被销毁，然而闭包中不是这样，局部变量生命周期被延长，闭包将使这些数据无法及时销毁，会占用内存，容易造成内存泄漏。如：
```

    function addHandle() {
        var element = document.getElementById('myNode');
        element.onclick = function() {
            alert(element.id);
        }
    }
```
此处，onclick匿名函数保存了一个对包含函数活动对象（变量对象）的引用，其保存element的引用，element将不会被回收。
```

        function addHandle() {
        var element = document.getElementById('myNode');
        var id = element.id;
        element.onclick = function() {
            alert(id);
        }
        element = null;
    }
```
此处将element设为null，即解除对其的引用，垃圾回收器将回收其占用内存。

此篇对JavaScript闭包做了总结，阐述，限于篇幅，在下篇讲述JavaScript中的高阶函数。