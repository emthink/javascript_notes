# Backbone.js入门
基于对Backbone.js的学习与几个项目实践，从本篇开始介绍Backbone.js，希望给看官提供一些帮助，同时巩固、提升已有知识。经过这些年的发展，前端MVC、MV*模式框架都得到了巨大的发展，如Backbone.js、AngularJs。在介绍Backbone之前有必要先了解一下MVC。

# MVC
MVC，即Model-View-Controller，是一种架构设计模式，强调通过分离关注点以改进程序的组织结构，通过将业务数据与用户界面分离、由另外的组件控制逻辑、用户输入、模型与视图的交互实现关注点分离。

# Backbone
经过不断发展与尝试，JavaScript开发人员将MVC模式融进了前端开发，并提出了多个使用MVC的JavaScript框架，如接下来要介绍的Backbone。

## 模型
不同框架中模型功能多少会有些不同，但一般都会有表示模型数据的属性。

- Backbone模型是一种持久化模型，允许对保存的模型进行修改，更新，常见的是在浏览器localStorage保存或与数据库同步。
- Backbone通常将模型进行分类分组，在分组中管理具有同一类特征的模型，这些分组就是集合，集合可以对模型进行许多操作。
- Backbone中通常有一个或多个视图观察同一模型，模型更新时，视图获得通知，进行视图更新

## 集合
集合是模型的有序组合，一般通过对同一性质或类型模型进行分组归入不同集合以集中管理模型，但集合并不是必须的。

- 集合可以很方便的添加和删除、更新模型，检索模型，并能对模型进行批量管理。
- 集合通过绑定change事件可以监听到集合中模型的变化，并执行相应监听函数。

## 视图
在程序中，用户一般只与用户界面（即视图）交互，用户通过视图对模型进行操作。

- Backbone在视图中定义render()方法，一般使用JavaScript模板引擎渲染模型内容，生成视图。
- Backbone模型构造函数（initialize()方法）中添加render()回调方法，在模型内容变更时触发更新视图。
- Backbone视图中events对象采用事件委托形式为视图元素绑定监听事件回调函数。

## 模板
相对于最初通过字符串拼接，在内存中创建大量HTML标记，然后便利数据插入，耗费大量成本的这种不明智方式，JavaScript模板库通常将视图模板定义为包含模板变量的HTML标记。模板可以保存在script标签或外部文件模板中，变量通过变量语法分隔；以多种格式接收数据，常见的是JSON格式。

如Underscore.js模板：

```

    <div>
        <h1><%= title %></h1>
        <p><%= description %></p>
        <p><%= price %></p>
        <div class="buy <%= num > 0 ? 'buy' : 'disabled' %>"></div>
    </div>
```

## 事件Event
Backbone事件Event，将事件触发后要执行的函数注册成事件处理句柄，事件发生便触发执行该函数。

- 可以将业务逻辑与用户界面关注点分离，即业务逻辑不需要知道界面工作流程。
- Backbone.Events对象混入Backbone模型、集合、路由、视图等诸多对象中。
- Backbone.Events可以让任何对象具有绑定和触发事件的能力。

```

    //引入underscore.js...
    var obj = {};
    _.extend(obj, Backbone.Events);
    
    obj.on('eat', function(msg) {
        console.log(msg);
    });
    obj.trigger('eat', 'eat food');
```

## 路由
严格来说，Backbone中并没有MVC中的控制器，通常在视图中包含控制器逻辑，而使用路由管理应用程序状态，在不刷新页面的同时通过监听文档url的hash片段变化，导向不同的视图和模型。

- Backbone支持动态路由选择，允许定义带参数的路由，可以根据参数在路由回调函数中进行不同处理。
- Backbone以Backbone.history作为路由的入口，无论是点击浏览器前进后退按钮还是手动使用navigate导航，都是从history入口，根据定义好的路由匹配规则进入路由回调函数，实现功能。

本篇主要介绍了一下Backbone.js的主要特征及主要模块，对Backbone有一个综合的了解，下篇开始学习Backbone具体模块。