#JavaScript异步编程
JavaScript语言执行环境是单线程的。
##JavaScript之单线程

---
单线程在程序执行时，所走的程序路径按照连续顺序排下来，前面的必须处理好，后面的才会执行。在某个特定的时刻只有特定的代码能够被执行，且会阻塞其它的代码。

> 单线程就是一心一意，用情专一的痴情少年。单线程就是进程只有一个线程；多线程就是进程有多个线程。

基于JavaScript的单线程语言执行环境及其不足，提出了JavaScript的同步与异步编程方式。

>同步，即任务一步一步执行，当前代码执行完毕后才能继续执行后续代码；异步则是开始执行当前代码后不需要等待，即可继续执行后续任务，当前任务执行结束后可以通过状态、通知和回调来通知调用者。

##JavaScript之异步编程实现

---
JavaScript异步编程实现主要归为三类：回调函数、发布订阅、Promise对象。

###回调函数



```

    function a(fn) {
        //a函数任务...
        setTimeout(function() {
            fn();
        }, 0);
        console.log('a');
    }
    function b(fn) {
        //b函数任务
        console.log('b');
    }
    a(b);
```
以上代码打印值顺序如何呢？结果是先a后b。至于为什么，不得不说说setTimeout。再细微的东西，只要存在，我们都能从中弄出些精华，就比如setTimeout。
请看代码：
···
    
    for (var i = 0; i < 5; i ++) {
        setTimeout(function(){
            console.log(i);
        }, 0);
    }
    console.log(i);
    //5 ; 5 ; 5 ; 5; 5
···
全部打印出5，要明白为什么，需理解三点：

1. i在此处是for循环所在上下文环境的变量，有且只有一个i;
2. 循环结束时i==5;
3. JavaScript单线程事件处理器在线程空闲前不会执行下一事件。

*[可参阅本系列笔记JavaScript之setTimeout与setInterval]()*
你可能看过类似这样的代码：

···

    function f1(callback) {
        setTimeout(function() {
            f2(functions() {
                setTimeout({    
                   callback(...) ;
                }, 0); 
            });
        }, 0);
    }
    function(callback) {//...}
···
请避免这样的多层嵌套回调。
###发布订阅

PubSub,即Publish/Subscribe,发布、订阅模式， 用以分发事件。常见的有jQuery的自定义事件监听、Node的EventEmitter对象等。

- **jQuery事件监听**

```

    $('#btn').on('myEvent', function(e) {
        console.log('There is my Event');
    });
    $('#btn').trigger('myEvent');
```

- **PubSub**

```
    
    var PubSub = function(){
        this.handlers = {}; 
    };
    PubSub.prototype.subscribe = function(eventType, handler) {
        if (!(eventType in this.handlers)) {
            this.handlers[eventType] = [];
        }
        this.handlers[eventType].push(handler); //添加事件监听器
        return this;//返回上下文环境以实现链式调用
    };
    PubSub.prototype.publish = function(eventType) {
        var _args = Array.prototype.slice.call(arguments, 1);
        for (var i = 0, _handlers = this.handlers[eventType]; i < _handlers.length; i++) {
            _handlers[i].apply(this, _args);//遍历事件监听器
        }
        return this;
    };
    var event = new PubSub;//构造PubSub实例
    event.subscribe('list', function(msg) {
        console.log(msg);
    });
    event.publish('list', {data: ['one,', 'two']});
    //Object {data: Array[2]}
```
以上代码就是一个简易的订阅发布模式基本实现，还可以对其进行完善，如添加只执行一次事件监听器等。

###Promise对象

Promise是CommonJS的规范之一，拥有resolve、reject、done、fail、then等方法，能够帮助我们控制代码的流程。

> 1. 一个promise可能有三种状态：等待（pending）、已完成（fulfilled）、已拒绝（rejected）;
2. 一个promise的状态只可能从“等待”转到“完成”态或者“拒绝”态，不能逆向转换，同时“完成”态和“拒绝”态不能相互转换;
3. promise必须实现then方法（可以说，then就是promise的核心），而且then必须返回一个promise，同一个promise的then可以调用多次，并且回调的执行顺序跟它们被定义时的顺序一致;
4. then方法接受两个参数，第一个参数是成功时的回调，在promise由“等待”态转换到“完成”态时调用，另一个是失败时的回调，在promise由“等待”态转换到“拒绝”态时调用。
5. 同时，then可以接受另一个promise传入，也接受一个“类then”的对象或方法，即thenable对象。   

<br />
* **jQuery之Promise**

```
    
    var prom = new $.Deferred();
    prom.done(function(value) {
        console.log(value + ' Done');
    });
    prom.fail(function(value) {
        console.log(value + ' Fail');
    });
    prom.always(function(v) {
        console.log(' always here');
    });
    
    prom.resolve('OK');
```

**Deferred是Promise的超集，相对于Promise只提供then方法添加调用，触发这些调用需要其他支持，使用Deferred实例可直接触发调用。**

* **Promise简单实现**

```

    function defer() {
    		var tasks = [],
			    progresses = [],
				state = 'pending',
				value,
				reason;
			return {
				resolve: function(_value) {
					if (tasks) {
						value = ref(_value);
						tasks.forEach(function(task) {						
							nextTick(function() {
								value.then.apply(value, task);
							});
						});
						tasks = null;
						srare = 'resolved';
					}else {
						if (state === 'resolved') {
							throw new Error('A promise should been resolved once.');
						}
					}
				},
				reject: function(reason) {
					if (tasks) {
						value = ref(reason);
						tasks.forEach(function(task) {
							nextTick(function() {
								value.then.apply(value, [task[1], task[0]]);
							});
						});
						tasks = undefined;
						state = 'rejected';
					}else {
						if (state === 'rejected') {
							throw new Error('A promise should been rejected once.');
						}
					}
				},
				notify: function(progress) {
					if (state === 'resolved' || state === 'rejected') {
						return;
					}
					progresses.push(progress);
				},
				promise: {
					then: function(_callback, _errback, _notifyback) {
						var deferred = defer();
						_callback = _callback || function(value) {
							return value;
						};
						_errback = _errback || function(reason) {
							return reject(reason);
						};
						_notifyback = _notifyback || function(progress) {
							return progress;
						};
						var callback = function(value) {
							var result;
							try {
								result = _callback(value);
							}catch(e) {
								deferred.reject(e);
							}finally {
								deferred.resolve(result);
							}
						};		
						var errback = function(reason) {
							var result;
							try {
								result = _errback(reason);
							}catch(e) {
								deferred.reject(e);
							}finally {
								deferred.resolve(result);
							}
						}
						nextTick(function() {
							while (progresses.length) {
								try {
									_notifyback(progresses.shift());
								}catch(e) {
									deferred.reject(e);
									break;
								}
							}
						});				
						if (tasks) {
							tasks.push([callback, errback]);
						}else {
							nextTick(function() {
								if (state === 'rejected') {
									value.then(errback);
								}else if (state === 'resolved') {	
									value.then(callback);
								}
							});
						}
						return deferred.promise;
					}
				}
			};
		}
        var ref = function(value) {
			if (value && typeof value.then === 'function') {
				return value;
			}
			return {
				then: function(callback) {
					return ref(callback(value));
				}
			}
		};
		var reject = function(reason) {
			return {
				then: function(callback, errback) {
					return ref(errback(reason));
				}
			};
		};
		var nextTick = function(callback) {
			setTimeout(callback, 0);
		};
        //Promise实例
        var deferred = defer(),
    		promise = deferred.promise;
        promise.then(function(value) {
    	    console.log(value);
		    throw 'zhangsan';
		}).then(function (v) {
			console.log(v);
		}, function (err) {
		    console.error(err);
		    done();
		});
		setTimeout(function () {
		    deferred.resolve('zhangsan1');
		}, 1000);
```
以上为一个简单的Promise基本功能实现，比较基础，[*更多详情请查看本系列笔记JavaScript之Promise实现*]()。

此篇篇幅较长，颇费心力，待后再加完善。
