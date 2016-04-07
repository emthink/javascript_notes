# Backbone入门之模型

Backbone模型包含应用程序的数据和与数据相关的逻辑处理，我们通过拓展Backbone.Model对象来创建模型：

```

    var MenuModel = Backbone.Model.extend({});
```

## 构造函数初始化

当创建一个模型的新实例时，其构造函数，即initialize()方法会被调用：

```

    var MenuModel = Backbone.Model.extend({
        initialize: function() {
            console.log('Initialize the Model.');
        }
    });
    var menu = new MenuModel();
```

## 默认值
创建Backbone模型时，Backbone允许我们为模型设置一组默认值，通过defaults属性即可设置；当实例化模型时，可传入一个参数对象，此对象即为模型的数据，传入参数值将覆盖所设置的默认值。

```

    var MenuModel = Backbone.Model.extend({
        defaults: {
            title: 'this is a menu',
            name: '最近会话',
            status: 'active'
        }
    });
    var menu = new MenuModel({
        status: 'inactive'
    });
    console.log(JSON.parse(menu));
    //{"title": "this is a menu", "name": "最近会话", "status": "inactive"}
```

## 属性操作

- Model.get()

    Model.get()方法用于访问模型的属性：
    
```

    var MenuModel = Backbone.Model.extend({
        defaults: {
            title: 'this is a menu',
            name: '最近会话',
            status: 'active'
        }
    });
    var menu = new MenuModel({
        status: 'inactive'
    });
    console.log(menu.get('status'));//inactive
```

- Model.set()

    Model.set()方法用于设置模型的一个或多个属性值：
    
```

    var MenuModel = Backbone.Model.extend({
        defaults: {
            title: 'this is a menu',
            name: '最近会话',
            status: 'active'
        }
    });
    var menu = new MenuModel({
        status: 'inactive'
    });
    console.log(menu.get('status'));//inactive
    menu.set({
        status: 'active',
        title: 'menu'
    });
    console.log(menu.get('status'));//active
```

- Model.attributes

    Backbone模型的attributes属性，是包含模型状态的一个内部散列表，使用Model.attributes可直接访问该模型所有数据，其通常以服务器返回的JSON对象数据形式存在：
    
```
    
    var MenuModel = Backbone.Model.extend({
        defaults: {
            title: 'this is a menu',
            name: '最近会话',
            status: 'active'
        }
    });
    var menu = new MenuModel({
        status: 'inactive'
    });
    console.log(menu.attributes);
    //Object {status: "inactive", title: "this is a menu", name: "最近会话"} 
```

**注：强烈建议使用set方法更新模型attributes对象而不是直接修改attributes对象。**

- Model.toJSON()

    Model.toJSON()方法返回模型attributes对象拷贝副本的JSON格式对象：
    
```

    var MenuModel = Backbone.Model.extend({
        defaults: {
            title: 'this is a menu',
            name: '最近会话',
            status: 'active'
        }
    });
    var menu = new MenuModel({
        status: 'inactive'
    });
    console.log(menu.toJSON());
    //Object {status: "inactive", title: "this is a menu", name: "最近会话"}
    console.log(JSON.stringify(menu));
    //{"status":"inactive","title":"this is a menu","name":"最近会话"} 
```

**注：当给JSON.stringify()传递带toJSON()方法的对象时，其处理的是该对象执行toJSON()方法后返回的值，而不是原始传入的对象。**
            