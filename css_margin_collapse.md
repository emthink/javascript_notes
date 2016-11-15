# CSS外间距崩塌(margin collapse)

CSS中块盒子的上下外间距有时会崩塌或折叠成一个单一的外间距，其值为发生崩塌的上下外间距中的最大值。
发生外间距坍塌的情况可分为如下三种，当然，都是在同一个[块格式化上下文(Block Formatting Context，简称BFC)](http://blog.codingplayboy.com/2016/11/05/css_formattingcontext/)中才会发生margin折叠：

## 相邻的兄弟节点

相邻兄弟节点的外间距会发生崩塌，除非后一个兄弟节点发生浮动。
如：

```

    <p>看我的margin-bottom:50px;</p>
    <p>看我的margin-top:30px.</p>
```

其样式：

```

    p {
        margin-top: 30px;
        margin-bottom: 50px;
    }
```
效果如图：

![CSS Margin Collapse](http://blog.codingplayboy.com/wp-content/uploads/2016/11/css_collapse.png)
 
上例中发生了margin折叠，两个p元素的maegin-top和margin-bottom折叠，形成一个外间距，其值为两者中的最大值,为50px；当然你可以把其中一个值改为负值试试：

```

    <p>看我的margin-bottom:50px;</p>
    <p style="margin-top: -20px;">看我的margin-top:30px.</p>
```
第二个p元素margin-top为负值，效果如图：

![一正一负margin](http://blog.codingplayboy.com/wp-content/uploads/2016/11/css_negative_collapse.png)

如上图，也发生了margin折叠，形成一个外间距，其值为最大的正值加上最小的负值（-10px > -20px）,所以其值为50 + （-20）= 30px.
 
再看如下例子
```

    <p>看我的margin-bottom:50px;</p>
    <p class="left">看我的margin-top:30px.</p>
```

其样式如下：

```

    <style media="screen">
        p {
            margin-top: 30px;
            margin-bottom: 50px;
        }
        .left {
            float: left;
        }
    </style>
```
其效果如图：

![无margin collaspe](http://blog.codingplayboy.com/wp-content/uploads/2016/11/css_no_collapse.png)

上图中两个p元素外间距为50 + 30 = 80px，没有发生margin折叠，是因为第二个p元素发生float:left;左浮动，形成一个独立的[块格式化上下文（Block Formatting Context，简称BFC）](http://blog.codingplayboy.com/2016/11/05/css_formattingcontext/)。
 
## 父节点和第一个或最后一个子节点

这种情况发生坍塌分为两种，一种margin-top坍塌，一种是margin-bottom坍塌：

- 1.不存在边框，内间距，行内内容或浮动等来分隔一个块级盒子的margin-top和其第一个子块级盒子的margin-top
- 2.不存在边框，内间距行内内容，高度，最小高度，或最大高度等来分隔一个块级盒子的margin-bottom和其最后一个子盒的margin-bottom

## 空块

如果不存在边框，内间距，行内内容，高度，最小高度等来分隔一个块级盒子的margin-top和margin-bottom，则会发生坍塌。

## 合并后margin值

以上发生外间距坍塌的情况，合并后margin值有两种：

- 1.当margin均为正值时，合并后的外间距为其中的最大值；
- 2.若某一外间距为负值，则合并后的外间距为最大的正值加上最小的负值

## 避免margin坍塌

有以下两种方式避免margin坍塌：

- 1.**浮动定位和绝对定位的元素不会发生外检局坍塌。**
- 2.**一个块级格式化上下文中的父子节点或兄弟节点之间，如果任一节点形成一个独立的块格式化上下文，则不发生margin坍塌。**

本文所有源码[https://github.com/codingplayboy/web_demo/blob/master/css/css_collapse.html](https://github.com/codingplayboy/web_demo/blob/master/css/css_collapse.html)。

 