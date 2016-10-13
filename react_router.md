# React入门与进阶之路由

在传统的网页应用中，一般是根据用户的操作指向不同的url，然后服务器渲染出不同的html代码，后来有了ajax，在同一页面里，可以为不同操作，指定处理器函数，在不刷新页面的情况下更新局部视图，但是局限依然较大，一旦跳转了URL，依然需要服务器渲染模板返回；而在Backbone，Angular，React出现以后，在单页面应用中，我们可以给不同URL指定处理器函数，保持URL与视图的同步，渲染模板的功能已经转移到客户端进行，与服务器的交互只涉及到数据，这就是路由的功能。

## React中的路由

React是一个用户界面类库，仅相当于MVC模式中的V-view视图，其本身并不包含路由功能，但是它以模块的方式提供了路由功能，可以很好的与React进行协作开发，当然这也并不是必须使用的。现在很多单页应用框架都实现了各自的路由模块，如Backbone，Angular等。而这其中很多路由模块也能与React搭配使用。本文将介绍本人使用过的两种：Backbone.Router和react-router。

不使用路由模块，我们需要处理一个展示笔记列表和展示特定笔记对应不同视图时，React处理视图更新的方式大致如此：

```
    
    // 状态变量
    var NOTINGSTATUS = {
        SHOW: 0,
        EDIT: 1
    };
    
    // 模拟数据
    var notings ={
        10001: {
            id: 10001,
            title: 'React',
            content: 'React Router',
            status: NOTINGSTATUS.SHOW
        },
        10002: {
            id: 10002,
            title: 'Backbone',
            content: 'Backbone Router',
            status: NOTINGSTATUS.EDIT
        }
    };
    
    // React-Noting应用入口组件
    var NotingApp = React.createClass({
        getInitialState: function() {
            return {
                
            };
        },
        render: function() {
            return (
                <div>
                    <h1>React Noting</h1>
                    <NotingList notings={this.props.notings.slice(0, this.props.indexNotingListLength)}/>
                </div>
            );
        }
    });
    
    // 展示列表组件
    var NotingList = React.createClass({
        getNotings: function() {
            return notings;
        },
        getInitialState: function() {
            return {
                notings: this.props.notings || this.getNotings()
            };
        },
        render: function() {
            var notings = this.state.notings;
            var _notingList = [];
            
            for (var key in notings) {
                _notingList.push(<NotingItem key={key} noting={notings[key]} />);
            }
            return (
                <div>
                    <ul>{_notingList}</ul>
                </div>
            );
        }
    });
    // 展示特定项组件
    var NotingItem = React.createClass({
        getInitialState: function() {
            return {
                noting: this.props.noting,
                status: this.props.noting.status
            };
        },
        editNote: function() {
            this.setState({
                status: NOTINGSTATUS.EDIT;
            });
        },
        render: function() {
            var status = this.state.status;
            var noting = this.state.noting;
            
            if (status === NOTINGSTATUS.EDIT) {
                return (
                    <li>
                        <input type="text" value={noting.title} autoFocus={true} />
                    </li>
                );
            }else if (status === NOTINGSTATUS.SHOW) {
                return (
                    <li>
                        <h3 onClick={this.editNote}>{noting.title}</h3>
                    </li>
                );
            }
        }
    });

    var indexNotingListLength = 3;
    React.render(
        <NotingApp notings={notings} length={indexNotingListLength} />,
        document.querySelector('.noting-wrap')
    );
```

如上，没有路由模块管理，只是通过返回的组件状态变更，更新对应视图，随着应用越来越大处理会越发繁琐，接下来我们通过使用路由管理URL和视图的同步的方式来达到相同目的。

## Backbone.Router

Backbone是一种MVC（Model-View-Controller）模式的框架，其路由模块属于Controller层，独立于其他模块，可以很好的与React搭配使用，之前的例子使用Backbone.Router实现后如下：

```

    var NOTINGSTATUS = {
        SHOW: 0,
        EDIT: 1
    };
    var NOTINGROUTERTYPE = {
        LIST: 0,
        ITEM: 1
    };
    
    var NotingRouter = Backbone.Router.extend({
        routes; {
            '/': 'index',
            'notings': 'showNotings',
            'notings/:id': 'showNoting'
        },
        render: function(type, extraData) {
            if (type === NOTINGROUTERTYPE.LIST) {
                React.render(
                    <NotingList notings={notings} />,
                    document.querySelector('.noting-wrap')
                );
            }else if (type === NOTINGROUTERTYPE.ITEM) {
                React.render(
                    <NotingItem noting={notings[extraData.id]} />,
                    document.querySelector('.noting-wrap');
                );
            }
        },
        index: function() {
            
        },
        showNotings: function() {
            this.render(NOTINGROUTERTYE.LIST);
        },
        showNoting: function(id) {
            this.render(NOTINGROUTERTYPE.ITEM, {id: id});
        }
    });
    
    var notingRouter = new NotingRouter();
    Backbone.history.start();
```

如上，如果访问一个如/notings的URL地址，将会展示所以noting，而访问诸如/notings/123这种的URL则会展示特定id为123的noting。

了解更多关于Backbone路由的信息，可以查看[http://backbonejs.org/#Router](http://backbonejs.org/#Router)。

## react-router

**注：react-router 1.0.x和0.13.x两个大版本之前API语法有差别，本文使用最新的语法介绍react-router。**

我们在前文了解了Backbone.Router如何作为路由模块与React搭配使用，现在继续学习react-router。

不同于Backbone的路由模块，react-router完全由React组件（component）构成：路由本身被定义成组件使用，而其内部路由分发处理器也定义成组件的形式使用。

现在让给我们继续使用react-router来实现之前展示noting的例子的路由管理：

```
    
    var Router = require('react-router').Router;
    var Route = require('react-router').Route;
    var Link = require('react-router').Link;
    
    // React-Noting应用入口组件
    var NotingApp = React.createClass({
        getInitialState: function() {
            return {
                
            };
        },
        render: function() {
            return (
                <div>
                    <h1>React Noting</h1>
                    {this.props.children}
                </div>
            );
        }
    });
    
    // 展示列表组件
    var NotingList = React.createClass({
        getInitialState: function() {
            return {
                notings: this.props.notings
            };
        },
        render: function() {
            var notings = this.state.notings;
            var _notingList = [];
            
            for (var key in notings) {
                _notingList.push(<NotingItem key={key} noting={notings[key]} />);
            }
            return (
                <div>
                    <ul>{_notingList}</ul>
                    <Link to="/notings">Show More</Link>
                </div>
            );
        }
    });
    // 展示特定项组件
    var NotingItem = React.createClass({
        getNoting: function() {
            return notings[this.props.params.id]
        },
        getInitialState: function() {
            var noting = this.props.noting || this.getNoting();
            
            return {
                noting: noting,
                status: noting.status
            };
        },
        editNote: function() {
            this.setState({
                status: NOTINGSTATUS.EDIT;
            });
        },
        render: function() {
            var status = this.state.status;
            var noting = this.state.noting;
            
            if (status === NOTINGSTATUS.EDIT) {
                return (
                    <li>
                        <input type="text" value={noting.title} autoFocus={true} />
                    </li>
                );
            }else if (status === NOTINGSTATUS.SHOW) {
                return (
                    <li>
                        <h3 onClick={this.editNote}>{noting.title}</h3>
                        <Link to="/noting/{noting.id}">Read More</Link>
                    </li>
                );
            }
        }
    });
    
    // 路由
    var NotingRouter = (
        <Router>
            <Route path="/" component={NotingApp}>
                <Route path="notings" component={NotingList}>
                    <Route path="/:id" component={NotingItem} />
                </Route>
                <Route path="*" component={notFound} />
            </Route>
        </Router>
    );
    
    React.render(
        NotingRouter,
        document.querySelector('.noting-wrap')
    );
```




