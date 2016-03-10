#Js模块化AMD与CMD

不同于传统的代码开发，作为一名前端开发者，不知道AMD或CMD是不合格的，我们有必要对它进行学习。在开始阐述AMD/CMD之前，先得明白什么是模块化。

> 模块化，利用分类思维把复杂的系统分解为结构合理、可拓展、便于管理的多模块，使各模块高内聚、低耦合。

## AMD

### CommonJs
在学习AMD之前，很有必要了解一下CommonJs，CommonJs是NodeJs的模块系统规范，其使用方式很简单，一个模块即是一个文件，如下：

定义模块b.js：
```

    //b.js
    var name = 'xjg';
    function getName() {
        return name;
    }
    //module.exports是CommonJs模块定义API
    module.exports = { 
        getName: getName
    };
    //or 
    exports.getName = getName;
    
```
a.js加载依赖模块b.js：
```

    //a.js
    var b = require('b');//可不加后缀.js
    b.getName();//调用
```
如上代码，调用b.getName()之前，需先加载依赖模块，并将其返回对象赋值给变量b，必须加载完成后，才调用b.getName()。在服务器环境，由于模块文件都在本地，其加载都是很快的，并不会感觉到阻塞；然后在客户端环境下，模块文件不在本地，加载模块文件需要耗时，则会明显阻塞后续代码，显然CommonJs不适用于浏览器环境。

### AMD

> AMD(Asynchronous Module Definition)，即异步模块定义，使用异步方式加载模块，加载模块，不阻塞后续代码的执行，依赖此模块的代码都在回调函数中，加载完此模块才运行。

- define()

    ```define(id?, dependencies?, factory);```
    
    |参数                         |说明
    |-----------------------------|------------
    |id(String)                   |定义模块标识id
    |dependencies(Array)          |模块其他依赖
    |factory(function|Object)     |定义模块初始化执行函数或对象
    
    - id
    
        如果模块 "a/b/c" 请求 "../d", 则解析为"a/d"; 
        
    	如果模块 "a/b/c" 请求 "./e", 则解析为"a/b/e"
    
    - factory
    
        模块初始化要执行的函数或对象。如果为函数，它只被执行一次。如果是对象    ，此对象为模块的输出值。

回调函数：
```

    define('b', [c], function(c) {
        var name = '惊鸿';
        function getName() {
            return name;
        }
        return {
            getName: getName
        }
    });
```
返回对象：
```

    define({
        name: '惊鸿',
        getName: function getName() {
            return this.name;
        }
    });
```

- 三个特殊关键字(require, exports, module)

    如果require,exports, 或 module在依赖列表中，参数应该按照
CommonJS模块规范自由变量去解析。

    *exports 仅仅是 module.exports 的一个引用。在 factory 内部给 exports 重新赋值时，并不会改变 module.exports 的值。*

```

    define('a', ['require', 'exports', 'module'], function(require, exports, module) {
        var b = require('b');
        exports.getName = function() {
            return b.getName();
        };
        //or
        module.export = {
            getName: function() {
                return b.getName();
            }
        };
    });
```

- require()

    ```require(id, callback);```
    
    |参数                         |说明
    |-----------------------------|------------
    |id(Array)                    |加载模块标识id，以逗号分隔
    |callback(function)           |模块加载后执行回调函数

```

    require(['a'], function(a) {
        a.getName();
    });
```

- define.amd 属性（Object）

    一个空对象，可用来判定当前页面是否支持AMD模块加载，如：
    
    ```
        
        //  支持AMD模块加载
        if (typeof define === 'function' && define.amd) {}
    ```

## CMD

CMD(Common Module Definition)，通用模块定义，在 Sea.js 中，所有 JavaScript 模块都遵循CMD模块定义规范。该规范明确了模块的基本书写格式和基本交互规则，在CMD中，一个模块就是一个文件。

- define()

    ```define(factory);```

    全局define函数，定义模块，factory为定义初始化执行对象，可以是函数、json对象、字符串。

    构造函数：
    ```
    
        define(function(require, exports, module) {
            var b = require('b');
            exports.getName = function() {
                return b.getName();
            };
            //or
            module.exports = {
                getName: function() {
                    return b.getName();
                }
            };
        });
    ```
    字符串定义模版：
    ```
    
        define("My name is {{name}}");
    ```
    对象定义数据：
    ```
    
        define({name: '惊鸿', tag: 'web developer'});
    ```

- require()

    ```require(id);```
    require函数接受一个模块标识参数，该函数返回传入参数对应模块提供的接口，其加载模块为同步加载：
    ```
    
        var b = require('./b');
        b.getName();
    ```
    ```require.async([ids], callback?);```
    require.async接受一个模块标识列表，一个模块加载完成后的回调函数，其加载模块为异步加载：
    ```
    
        require(['./a', './b'], function(a, b) {
            a.getName();
            b.getName();
        });
    ```
- exports与module

    参考AMD中关键变量exports和module
   
- define.cmd 属性（Object）

    一个空对象，可用来判定当前页面是否支持CMD模块加载，如：
    
    ```
        
        //  支持CMD模块加载
        if (typeof define === 'function' && define.cmd) {}
    ```
    
## AMD与CMD
初步学习了AMD与CMD,接下来比较一下AMD与CMD:

- AMD
    - 1.RequireJS 在推广过程中对模块定义的规范化产出；
    - 2.一个文件中可定义多个模块；
    - 3.异步加载模块；
    - 4.提前执行，依赖前置;(模块依赖，提前书写)

- CMD
    - 1.SeaJs在推广过程中对模块定义的规范化产出；
    - 2.一个模块即是一个文件；
    - 3.默认为同步加载模块；
    - 4.延迟执行，依赖就近，职责单一;（模块依赖，用时书写）
    
若有不足，欢迎指正。