# Backbone入门之事件（Backbone.Events）

Backbone事件将事件触发所要执行的回调函数注册成事件处理句柄，当事件发生时，就触发该函数。

## Backbone.Events

Backbone.Events可以扩展到任何对象上，使其具有绑定和触发事件的能力。在绑定事件的回调处理函数之前，不需要定义事件，且可以传递参数：

```

    var myObj = {};
    
    _.extend(myObj, Backbone.Events);
    
    //添加一个自定义事件
    myObj.on('test', function(msg) {
        console.log('triggered ' + msg);
    });
    
    //触发自定义事件,输出"triggered test"
    myObj.trigger('test', 'test');
```

### on()

on(event, callback [,context])会在对象上绑定一个事件回调函数，回调函数this默认指向当前对象,事件发生时，回调函数就会被调用。单一事件时，第一个参数event值即为自定义事件名；而多个事件时，则可以为事件加上命名空间，以冒号分隔；第三个参数可以传入作为回调函数执行上下文，：

*注：特殊事件all，可以捕获对象上所有的事件触发。*

- 未注册all事件

    ```
    
        var myObj = {};
        _.extend(myObj, Backbone.Events);
        
        function test(msg) {
            console.log('triggered ' + msg);
        }
        
        myObj.on('test:first', test);
        myObj.on('test:second', test);
        
        //触发test:first事件，输出 "triggered test first"
        myObj.trigger('test:first', 'test first');
        //触发test:second事件，输出 "triggered test second"
        myObj.trigger('test:second', 'test second');
        
        //不触发任何事件
        myObj.trigger('test', 'test');
        
    ```
    
- 注册all事件

    ```
        
        var myObj = {};
        _.extend(myObj, Backbone.Events);
        
        function test(msg) {
            console.log('triggered ' + msg);
        }
        
        //监听所有事件
        myObj.on('all', test);
        
        //触发test:first事件，输出 "triggered test first"
        myObj.trigger('test:first', 'test first');
        //触发test:second事件，输出 "triggered test second"
        myObj.trigger('test:second', 'test second');
        
        //触发test事件， 输出"triggered test"
        myObj.trigger('test', 'test');
        
        //触发所有事件
    ```

### once()

注册事件并绑定回调处理函数，触发一次后立即被移除，其他同on()方法。

### off()

off([event] [,callback] [,context])事件可以移除之前通过on()方法绑定在事件对象上的回调处理函数，第一个参数为要移除的回调函数对应的事件名，若为空则移除所有事件的回调函数；第二个参数为对应的要移除的回调函数名，若为空则移除该事件所有绑定的回调函数：第三个函数为回调函数上下文，若为空则移除所有上下文下的这个回调函数：

```

    var myObj = {};
    _.extend(myObj, Backbone.Events);
    
    function test(msg) {
        console.log('triggered ' + msg);
    }
    function joke(msg) {
        console.log("Joke " + msg);
    }

    myObj.on('test:first', test);
    myObj.on('test:second', test);
    myObj.on('joke', test);
    myObj.on('joke', joke);
    
    myObj.off('test:first');

    //触发test:first事件，不输出
    myObj.trigger('test:first', 'test first');
    //触发test:second事件，输出 "triggered test second"
    myObj.trigger('test:second', 'test second');
    
    myObj.off('joke', test);
    //触发joke事件，输出"Joke joke"
    myObj.trigger('joke', 'joke');
    
    myObj.off('joke');
    //触发joke事件，不输出
    myObj.trigger('joke', 'joke');
```

### trigger()
trigger(event [,*args])方法为指定事件触发回调函数，第一个参数为事件名，后面的参数为传递的参数，可以为一个或多个；trigger()方法可以触发一个或多个事件的回调函数，触发单个事件时，event参数值即为事件名；而触发多个事件时，event值为以空格分隔的多个事件名：

```

    var myObj = {};
    _.extend(myObj, Backbone.Events);
    
    function test(arg1, arg2) {
        console.log('triggered ' + arg1 + ' ' + arg2);
    }
    function joke(msg) {
        console.log("Joke " + msg);
    }
    
    myObj.on('test', test);
    myObj.on('joke', joke);
    
    //输出"triggered boy girl"
    myObj.trigger('test', 'boy', 'girl');
    
    //输出"triggered several events undefined"和"Joke several events"
    myObj.trigger('test joke', 'several events');
```

### listenTo()

前文的on()和off()都是在对象上直接绑定或移除回调函数，而listenTo(obj, event, callback)方法则可以实现一个对象监听另一个对象上的事件，第一个参数是要监听的对象，第二个参数是要坚听对象上事件的事件名，第三个参数是所监听对象上事件触发时此对象上的回调函数：

*注：object.listenTo()方法调用时，其第三个参数回调函数执行上下文总是当前对象object。*

```

    var objA = {}, objB = {};
    _.extend(objA, Backbone.Events);
    _.extend(objB, Backbone.Events);
    
    objA.listenTo(objB, 'test', function(e) {
        console.log('listened it');
    });
    
    //输出"listened it"
    objB.trigger('test');
```

### listenToOnce()

listenToOnce(obj, event, callback)方法则可以实现一个对象监听另一个对象上的事件，并在该事件触发一次后

### stopListening()

stopListening([other] [,event] [,callback])方法使对象终止监听事件，若不带参数，则停止监听所有对象，移除所有已注册的回调函数；若第二个参数为空，则停止监听某对象上所有事件，移除为该对象注册的所有回调函数；若第三个参数为空，停止监听某对象上特定事件，移除为该事件注册的所有回调函数；参数均不为空，移除某对象上特定事件的指定回调函数：

```

    var objA = {}, objB = {};
    _.extend(objA, Backbone.Events);
    _.extend(objB, Backbone.Events);
    
    objA.listenTo(objB, 'test', function(e) {
        console.log('listened it');
    });
    
    //输出"listened it"
    objB.trigger('test');
    
    objA.stopListening(objB);
    //不输出
    objB.trigger('test');
```

## 事件与视图

在Backbone中，事件使用最多的场景总是与视图对象一起，在一个视图中，要监听事件，一般有两种方式：DOM事件和Event事件API。

- DOM事件

    添加DOM事件可以通过视图对象events属性或者jQuery.on()方法注册：
    
    - 使用events属性注册的事件，回调函数的this指向视图对象
    - 使用jQuery注册事件，回调函数this指向DOM元素
    
- Event API

    使用Event API注册事件也有两种情况：
    
    - 使用on()方法注册事件，上下文默认是指向当前对象，也可以通过第三个参数传入作为回调函数上下文
    - 使用listenTo()注册事件，回调函数this指向当前对象

