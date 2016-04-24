# Backbone入门之集合(Backbone.Collection)

[上一篇，我们讲述了Backbone模型的入门学习](http://blog.codingplayboy.com/index.php/2016/04/10/backbone_model/)，本篇开始学习Backbone集合。

Backbone集合是模型的有序组合，通过拓展Backbone.Collection对象来创建集合：

```

    var app = app || {};
    app.MenuModel = Backbone.Model.extend({
        defaults: {
            title: 'this is a menu',
            name: '最近联系人',
            status: 'active'
        }，
        idAttribute: 'idx'
    });
    app.MenuCollection = Backbone.Collection.extend({
        model: app.MenuModel
    });
    app.menu = new app.MenuModel({
        status: 'inactive',
        idx: '001'
    });
    app.menus = new app.MenuCollection([app.menu]);
    console.log(app.menus.length); //1
```
在创建集合时，通过model属性设置集合中的模型类型，实例化集合时，可以传入对象或数组，它们将被自动转换为通过model设置的模型类型。

## 初始化
在创建集合时，可以设置initialize函数，在实例化集合时，将自动调用执行initialize函数：

```

    app.MenuCollection = Backbone.Collection.extend({
        model: app.MenuModel,
        initialize: function() {
            console.log('集合初始化成功');
        }
    });
    app.menus = new app.MenuCollection([app.menu]);
```
打印值如下：

![初始化打印值](http://blog-resource.bj.bcebos.com/photos/2016/04/app-menus.png)

## 集合属性方法

- models

	此属性返回集合中模型数组：
	
	```
		app.MenuModel = Backbone.Model.extend({
	       	defaults: {
		       	title: 'this is a menu',
		       	status: 'inactive'
	       	}
    		});
		app.MenuCollection = Backbone.Collection.extend({
		      model: app.MenuModel,
		      initialize: function() {
		          console.log('集合初始化成功');
		      }
		});
		var menus = [
			{
				id: 1,
				name: '最近会话',
				status: 'active'
			},
			{
				id: 2,
				name: '通讯录',
				status: 'inactive'
			}
		]
		app.menus = new app.MenuCollection(menus);
		console.log(app.menus.models.length); //2
	```

- toJSON()

	Collection.toJSON()方法返回集合中所有模型attributes对象拷贝副本的JSON格式对象数组：
	
	```
		
		console.log(app.menus.toJSON());
	```
	![Collection.toJSON()](http://blog-resource.bj.bcebos.com/photos/2016/04/app-menus04.png)
	
## 集合与模型

Backbone.Collection对象允许我们通过多种方法对模型进行管理。

### 添加/移除模型
集合创建以后，可以使用add()和remove()方法添加或移除集合中的模型：

```

	app.MenuModel = Backbone.Model.extend({
       	defaults: {
	       	title: 'this is a menu',
	       	status: 'inactive'
       	}
       });
	app.MenuCollection = Backbone.Collection.extend({
	    model: app.MenuModel,
	    initialize: function() {
	        console.log('集合初始化成功');
	    }
	});
	var menu1 = new app.MenuModel({
		name: '最近会话',
		status: 'active'
	});
	var menu2 = new app.MenuModel({
		name: '通讯录',
		status: 'inactive'
	});
	var menu3 = new app..MenuModel({
		name: '公告',
		status: 'inactive'
	});
	app.menus = new app.MenuCollection([menu1, menu2]);
	console.log(app.menus);
```
app.menus打印值如下：

![app.menus集合](http://blog-resource.bj.bcebos.com/photos/2016/04/app-menus02.png)

```

	app.menus.add(menu3);
	console.log(app.menus.length);//3
	app.menus.remove(menu2);
	console.log(app.menus.length);//2
```

### 检索模型

- get()

	调用collection.get()方法，传入一个id或者cid从集合中检索一个模型：
	
	```
	
		app.MenuModel = Backbone.Model.extend({
	       	defaults: {
		       	title: 'this is a menu',
		       	status: 'inactive'
	       	}
    		});
		app.MenuCollection = Backbone.Collection.extend({
		      model: app.MenuModel,
		      initialize: function() {
		          console.log('集合初始化成功');
		      }
		});
		var menus = [
			{
				id: 1,
				name: '最近会话',
				status: 'active'
			},
			{
				id: 2,
				name: '通讯录',
				status: 'inactive'
			}
		]
		app.menus = new app.MenuCollection(menus);
		var a = app.menus.get(1);
		var b = app.menus.get('c2');
		console.log(a === b); //true
	```
	关于模型的id、cid、idAttribute属性可以参考上一篇文章[Backbone入门之集合](http://blog.codingplayboy.com/index.php/2016/04/10/backbone_model/)

- at()

	调用collection.at()方法，传入模型在集合中的索引值，at始终根据模型在插入集合时的索引返回值：
	
	```
		
		var a = app.menus.get(1);
		var c = app.menus.at(0);
		console.log(a === c); //true
	```
	
- where()

	调用collection.where()方法，传入模型属性，返回符合的模型数组：
	
	```
	
		var d = app.menus.where({name: '最近会话'});
		console.log(d);
	```
	打印值如下：
	
	![where](http://blog-resource.bj.bcebos.com/photos/2016/04/app-menus03.png)

- findWhere()

	findWhere()方法与where()方法调用方式一样，只是findWhere()方法返回匹配传入属性的第一个模型，而不是返回一个模型数组：
	
	```
	
		var d = app.menus.where({name: '最近会话'});
		console.log(d);
	```

### 重置刷新集合

- set()

	Backbone不仅支持添加或移除模型，还提供一次性更新整个集合：调用Collection.set()方法，接收一个模型数组，将执行更新集合所必要的添加、移除和更新操作。
	
	如果列表中的一个模型尚不在集合中，那么它将被添加; 如果模型已经在集合中，其属性将被合并; 并且如果集合包含不存在于列表中的任何模型，他们将被删除。 以上所有将触发相应的"add", "remove", 和 "change"事件。 返回集合中的模型。另外 可以设置选项：{add: false}, {remove: false}, 或 {merge: false}，将相应操作禁用，实现自定义行为。

	```
	
		app.MenuModel = Backbone.Model.extend({
	       	defaults: {
		       	title: 'this is a menu',
		       	status: 'inactive'
	       	}
	       });
		app.MenuCollection = Backbone.Collection.extend({
		    model: app.MenuModel,
		    initialize: function() {
		        console.log('集合初始化成功');
		    }
		});
		var menus = [
			{
				id: 1,
				name: '最近会话',
				status: 'active'
			},
			{
				id: 2,
				name: '通讯录',
				status: 'inactive'
			}
		]
		app.menus = new app.MenuCollection(menus);
		app.menus.set([
			{id:1, name: '最近聊天'},
			{id:3, name: '公告'}
		]);
		console.log(app.menus.length); //2
		console.log(app.menus);
	```
	打印值如下：
	
	![刷新集合](http://blog-resource.bj.bcebos.com/photos/2016/04/app-menus05.png)

	```
	
		app.menus = new app.MenuCollection(menus);
		app.menus.set([
			{id:1, name: '最近聊天'},
			{id:3, name: '公告'}
		], {remove: false});
		console.log(app.menus.length); //3
	```

- reset()

	调用Collection.reset()方法，可以重置整个集合：
	
	```
	
		app.MenuModel = Backbone.Model.extend({
	       	defaults: {
		       	title: 'this is a menu',
		       	status: 'inactive'
	       	}
	       });
		app.MenuCollection = Backbone.Collection.extend({
		    model: app.MenuModel,
		    initialize: function() {
		        console.log('集合初始化成功');
		        this.on('reset', function(cols, options) {
		        	console.log(cols, options.previousModels);
		        });
		    }
		});
		var menus = [
			{
				id: 1,
				name: '最近会话',
				status: 'active'
			},
			{
				id: 2,
				name: '通讯录',
				status: 'inactive'
			}
		]
		app.menus = new app.MenuCollection(menus);
		app.menus.reset([
			{id:1, name: '最近聊天'}
		]);
		console.log(app.menus.length); //1
	```
	打印值如下：
	![Collection.reset()](http://blog-resource.bj.bcebos.com/photos/2016/04/app-menus06.png)
	
	**调用Collection.reset()方法不会触发add和remove事件，但会触发reset事件，我们可以通过传入空参数来清空集合；监听reset事件时可以从options.previousModels访问之前的模型数组。**

## 事件监听
集合是模型的有序组合，在集合中添加或移除模型时会触发add或remove事件，我们可以监听这些事件，进行相应数据逻辑处理；同时，可以在集合的任意模型属性上绑定change事件，监听模型属性的变化。

**一般，事件监听在集合初始化时绑定，第一个参数为监听事件类型，第二个参数为监听回调，第三个参数为回调的上下文环境(可选)，通常传入指向当前集合的this。**

```

	app.MenuModel = Backbone.Model.extend({
       	defaults: {
	       	title: 'this is a menu',
	       	status: 'inactive'
       	}
    });
	app.MenuCollection = Backbone.Collection.extend({
	    model: app.MenuModel,
	    initialize: function() {
	        console.log('集合初始化成功');
	        this.on('reset', function(cols, options) {
	        	console.log('重置集合');
	        }, this);
	        this.on('add', function(model) {
	        	console.log('添加模型，cid： ' + model.cid);
	        }, this);
	         this.on('change', function(model) {
	        	console.log('模型属性改变为: ' + model.get(name'));
	        });
	        this.on('change:name', function(model) {
	        	console.log('模型name属性改变为: ' + model.get('name'));
	        });
	        this.on('remove', function(model) {
	        	console.log('移除模型: ' + model.cid);
	        });
	    }
	});
	var menus = [
		{
			id: 1,
			name: '最近会话',
			status: 'active'
		},
		{
			id: 2,
			name: '通讯录',
			status: 'inactive'
		}
	]
	app.menus = new app.MenuCollection(menus);
	app.menus.set([
		{id:1, name: '最近聊天'},
		{id:3, name: '公告'}
	]);
	app.menus.reset([
		{id:1, name: '最近聊天'}
	]);
	
	//logs:
	//模型name属性改变为: 最近聊天 
	//模型属性改变为: 最近聊天
	//移除模型: c3 
	//添加模型，cid： c4 
	//重置集合 
```

Backbone事件除了上文的on()方法，还可以使用once()方法，与jQuery的once类似，监听回调只执行一次。

关于Backbone.Collection集合的主要基础内容就阐述到这里，下一篇继续学习Backbone的视图（Backbone.View）。