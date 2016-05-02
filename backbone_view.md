# Backbone入门之视图(Backbone.View)

Backbone视图可以使用JavaScript模板，根据模型数据的逻辑处理向用户展示相应的界面。可以监听模型的change事件，并在回调函数绑定视图的render()方法，就可以在不重绘整个页面的情况下，更新视图。

## 创建视图

可以通过扩展Backbone.View对象创建一个视图：

```

	var app = app || {};
	app.MenuListView = Backbone.View.extend({
		tagName: 'ul',
		className: 'menu-list',
		template: function() {
			return _.template('<li><%= title %></li>');
		}, 
		render: function() {
			this.$el.html(this.template()({title: "Menu List"}));
			return this;
		},
		initialize: function() {
			this.render();
			$('body').append(this.$el);
		}
	});
	app.menulist = new app.MenuListView();
	console.log(app.menulist.el);
	console.log(app.menulist.$el);
```
打印值如下：

app.menulist.el:

![app.menulist.el](http://blog-resource.bj.bcebos.com/photos/2016/05/app-menus-views03.png)

app.menulist.$el:

![app.menulist.$el](http://blog-resource.bj.bcebos.com/photos/2016/05/app-menus-views01.png)

## 初始化

如果视图定义了initialize()初始化函数，在创建视图时，它将立即被调用。

### 视图与模型
创建视图时，可以通过传入model或collection属性和值，将某一模型或集合直接注册到视图中：

```
	
	var app = app || {};
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
	app.MenuListView = Backbone.View.extend({
		tagName: 'ul',
		className: 'menu-list',
		template: function() {
			return _.template('<li><%= title %></li>');
		}, 
		render: function() {
			this.$el.html(this.template()({title: "Menu List"}));
			return this;
		},
		initialize: function() {
			//console.log('视图初始化');
			this.render();
			$('body').append(this.$el);
		}
	});
	app.menulist = new app.MenuListView({
		collection: app.menus
	});
	console.log(app.menulist.collection);
```
打印如下：

![app.menulist.collection](http://blog-resource.bj.bcebos.com/photos/2016/05/app-menus-views02.png)

## 视图与DOM

我们希望通过使用Backbone视图生成页面文档，将页面展示给用户，就必须将视图与DOM关联起来，通过操作视图改变DOM。

- Backbone提供两种方式关联视图和DOM:

	- 创建
		
		创建视图时创建一个新元素，然后将该元素插入至DOM。
	- 引用
	
		视图直接引用页面已经存在的元素。

- 创建

	Backbone视图创建元素时只需要使用tagName、id、className属性。此Backbone视图对象的el属性是一个指向该元素的引用。
	
	*tagName默认值为div。*
	
	```
	
		app.MenuListView = Backbone.View.extend({
			tagName: 'ul', //元素标签类型，必选，未设置时默认为div
			className: 'menu-list', //元素class，可选
			id: 'menus', //元素id，可选
			initialize: function() {
				$('body').append(this.$el);
			}
		});
		app.menulist = new app.MenuListView();
		console.log(app.menulist.el);
		//logs: <ul id="menus" class="menu-list"></ul>
	```
	
	*注：此时Backbone视图生成的元素并没有插入到文档DOM。*

- 引用

	可以为Backbone视图对象传入一个el属性，来匹配页面文档已存在的元素。
	
	```
	
		app.MenuListView = Backbone.View.extend({
			el: 'body', //值为CSS选择器
			initialize: function() {
			}
		});
		app.menulist = new app.MenuListView();
		console.log(app.menulist.el);
		//logs: <body>...</body>
	```
	
	我们也可以在创建视图时，为对象el属性设置值：
	
	```
	
		app.menulist = new app.MenuListView({el: 'body'});
	```
	
	
	*el属性值为CSS选择器，Backbone视图对象将根据此选择器匹配页面元素。*
 
## 视图属性与方法
要深入理解Backbone视图，必须理解视图一些重要的属性和方法。

- el

	el是视图的一个核心属性，也是使用视图对象时必然会用到的一个属性。
	
	> el是DOM元素的一个引用，所有视图都有其el属性。视图可以通过使用el构成它的元素内容，在触发浏览器最少次数重排和重绘的情况下，将所有内容一次性插入文档DOM。
	
- $el、$()

	对视图和DOM操作，经常要用到jQuery函数，Backbone通过为视图定义$el属性和$()函数，为我们操作视图和DOM提供了很多便利。视图的$el属性等价于$(View.el)，$(selector)等价于$(View.el).find(selector)。

- setElement()	

	Backbone提供setElement()方法，支持将现有Backbone视图应用于不同的DOM元素，调用该方法将创建一个缓存$el引用，并且将视图委托事件从旧元素移动到新元素。
	
	```
	
		var MyView = Backbone.View.extend({
			events: {
				click: function(e) {
					console.log(myView.$el.html());
				}
			}
		});
		var btn1 = $('<button>button 1</button>');
		var btn2 = $('<button>button 2</button>');
		
		//视图元素el是btn1
		var myView = new MyView({el: btn1});
		
		btn1.trigger('click');  //logs: button 1
		
		//视图元素el指向btn2
		myView.setElement(btn2);
		
		btn1.trigger('click');
		btn2.trigger('click'); //logs: button 2
		console.log(myView.$el.html);// button 2
	```
	
## 渲染模板-render()