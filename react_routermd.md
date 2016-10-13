# React入门与进阶之路由

在传统的网页应用中，一般是根据用户的操作指向不同的url，然后服务器渲染出不同的html代码，后来有了ajax，在同一页面里，可以为不同操作，指定处理器函数，在不刷新页面的情况下更新局部视图，但是局限依然较大，一旦跳转了URL，依然需要服务器渲染模板返回；而在Backbone，Angular，React出现以后，在单页面应用中，我们可以给不同URL指定处理器函数，保持URL与视图的同步，渲染模板的功能已经转移到客户端进行，与服务器的交互只涉及到数据，这就是路由的功能。

## React中的路由

React是一个用户界面类库，仅相当于MVC模式中的V-view视图，其本身并不包含路由功能，但是它以模块的方式提供了路由功能，可以很好的与React进行协作开发，当然这也并不是必须使用的。现在很多单页应用框架都实现了各自的路由模块，如Backbone，Angular等。而这其中很多路由模块也能与React搭配使用。本文将介绍本人使用过的两种：Backbone.Router和react-router。

不使用路由模块时，我们需要处理一个笔记项展示和编辑状态对应不同视图时，React处理视图更新的方式大致如此：

```
    
    var NotingList = React.createClass({
        getInitialState: function() {
            return {
                notes: this.props.notes
            };
        },
        render: function() {
            return (
                <div>
                    <NotingItem note={}/>
                </div>
            );
        }
    });
    var NotingItem = React.createClass({
        getInitialState: function() {
            return {
                status: this.props.status,
                note: this.props.note
            };
        },
        editNote: function() {
            this.setState({
                status: NOTINGSTATUS.EDIT;
            });
        },
        render: function() {
            var status = this.state.status;
            var note = this.state.note;
            
            if (status === NOTINGSTATUS.SHOW) {
                return (
                    <div>
                        <input type="text" value={note.content} onFocus={true} />
                    </div>
                );
            }else if (status === NOTINGSTATUS.EDIT) {
                return (
                    <div>
                        <p onClick={this.editNote}>{note.content}</p>
                    </div>
                );
            }
        }
    });

    var NOTINGSTATUS = {
        SHOW: 0,
        EDIT: 1
    };
    var note ={
        id: 10001,
        content: 'React Router'
    };
    var tabs = React.render(
        <NotingItem status={NOTINGSTATUS.SHOW} note={note} />,
        document.querySelector('body')
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
    
    var NootingItemRouter = Backbone.Router.extend({
        routes; {
            'note/:id/': 'showNote',
            'note/:id/edit': 'editNote'
        },
        render: function(status) {
            if (!status || status === NOTINGSTATUS.SHOW) {
                React.render()
            }
        },
        showNote: function() {
            this.render()
        }
    });
```


