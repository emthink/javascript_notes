# 两端对齐布局与text-align:justify

前几天在项目开发过程中需要实现一个列表两端对齐，想着在页面开发过程中也经常会有需要用到两端对齐布局的时候，就计划总结一下常见实现方式，与诸位交流。

可以先看看实例效果：[两端对齐实现](http://demo.codingplayboy.com/demo/smallcase/htc/justify-layout.html)

[点此查看完整代码 https://github.com/codingplayboy/web_demo/blob/master/css/justify-layout.html](https://github.com/codingplayboy/web_demo/blob/master/css/justify-layout.html)

## 百分比实现

首先最简单的是使用百分比实现，如下一个展示列表：

```

    <div class="wrap">
        <ul class="percent-list">
            <li class="percent-item"><img /></li>
            <li class="percent-item"><img /></li>
            <li class="percent-item" style="margin-right: 0;"><img /></li>
        </ul>
    </div>
```

给该列表设置样式：

```

    .percent-list {
        width: 100%;
        background: #f0f0f9;
        list-style: none;
        font-size: 0;
    }

    .percent-item {
        display: inline-block;
        width: 30%;
        height: 60px;
        margin-right: 5%;
        border: 1px solid lightblue;
        box-sizing: border-box; /* 削去border边框宽度的影响 */
    }
```

效果如图：

![百分比实现两端对齐](http://blog.codingplayboy.com/wp-content/uploads/2016/10/justify-1.png)

这样当然很简单，而且也不会存在什么兼容性问题，但是其他问题还有很多：

- 定宽的列表，效果很难实现

- 如果内部img元素宽度不是100%且需要实现两端对齐，效果很难实现

- 如果列表项数量是动态的，效果不能实现


## flex实现

第二种实现方式就比较先进了，除了需要学习flex相关知识，使用还是很简单的，实现两端对齐效果，关键点是在flex容器上使用justify-content属性，并将其值设为space-between;

> Flex container

> The parent element in which flex items are contained. A flex container is defined using the flex or inline-flex values of the display property.

Flex容器，是flex项目的父元素，flex容器通过声明display属性为flex/inline-flex值产生。

---

> Flex item

> Each child of a flex container becomes a flex item. Text directly contained in a flex container is wrapped in an anonymous flex item.

Flex项目，flex容器的每个直接子元素都是一个flex项目，当直接子元素是文本时，该文本被一个匿名flex项目包含。
 
---

> The flex-direction property establishes the main axis.

> The justify-content property defines how flex items are laid out along the main axis on the current line.

> justify-content属性定义flex项目在当前行主轴线方向上的排布，而主轴由flex-direction属性定义。

*这两个属性均在flex容器上定义。*

更多关于flex知识请参考：[https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Using_CSS_flexible_boxes](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Using_CSS_flexible_boxes)，后续会对flex进行详细阐述。

如下一个列表结构

```

    <div class="flex-wrap">
        <h3>Flex两端对齐列表</h3>
        <ul class="flex-list">
            <li class="flex-item"><img src="http://blog.codingplayboy.com/wp-content/uploads/2016/06/cropped-profile-180x180.jpg" /><p>1</p></li>
            <li class="flex-item"><img src="http://blog.codingplayboy.com/wp-content/uploads/2016/06/cropped-profile-180x180.jpg" /><p>2</p></li>
            <li class="flex-item"><img src="http://blog.codingplayboy.com/wp-content/uploads/2016/06/cropped-profile-180x180.jpg" /><p>3</p></li>
            <li class="flex-item"><img src="http://blog.codingplayboy.com/wp-content/uploads/2016/06/cropped-profile-180x180.jpg" /><p>4</p><p>5</p></li>
        </ul>
    </div>
```

我们首先在flex容器设置样式：

```

    // 兼容将近所有智能手机
    .flex-list {
        display: -moz-box;
        display: -webkit-box;
        display: -webkit-flex;
        display: -moz-flex;
        display: -ms-flexbox;
        display: -ms-flex;
        display: flex;
        -webkit-box-pack: justify;
        -moz-box-pack: justify;
        -ms-flex-pack: justify;
        -webkit-justify-content: space-between;
        -moz-justify-content: space-between;
        -ms-justify-content: space-between;
        justify-content: space-between;
    }
```
 

在flex-item上设置display:inline-block;会使flex失效，于是最好声明：

```

    .flex-item{
        display:block
    }
```

**注：不可在flex项目上设置flex属性**

效果如图：

![flex两端对齐](http://blog.codingplayboy.com/wp-content/uploads/2016/10/flex-justify.png)

## text-align:justify实现

另外一种要介绍的是使用text-align:justyle;实现方式。

我们知道text-align:justify;效果是实现文字两端对齐,如。

```

    <p>默认是文字居中，如何实现文字两端对齐效果，这是个问题？</p>

    <p style="text-align: justify;">如何实现文字两端对齐效果，这是个问题？</p>
```

效果如图：

![文字两端对齐](http://blog.codingplayboy.com/wp-content/uploads/2016/10/text-justify.png)

那么我们可以利用该属性实现行内元素两端对齐嘛？答案是肯定的，但是我们得进行额外的处理。

一个设置justify状态下的列表：

```

    <div class="wrap">
        <h3>justify下列表</h3>
        <ul class="list">
            <li class="item"><img src="http://blog.codingplayboy.com/wp-content/uploads/2016/06/cropped-profile-180x180.jpg" /><p>1</p></li>
            <li class="item"><img src="http://blog.codingplayboy.com/wp-content/uploads/2016/06/cropped-profile-180x180.jpg" /><p>2</p></li>
            <li class="item"><img src="http://blog.codingplayboy.com/wp-content/uploads/2016/06/cropped-profile-180x180.jpg" /><p>3</p></li>
            <li class="item"><img src="http://blog.codingplayboy.com/wp-content/uploads/2016/06/cropped-profile-180x180.jpg" /><p>4</p><p>5</p></li>
        </ul>
    </div>
```
 
样式如下：

```

    .list {
        width: 100%;
        /*height: 90px;*/
        background: #f0f0f9;
        font-size: 0;
        text-align: justify;
        text-justify:distribute-all-lines;/* ie6-8 */
    }

    .item {
        width: 60px;
        height: 90px;
        display: inline-block;
        *display: inline;/* ie */
        *zoom: 1;
        box-sizing: border-box;
        -moz-box-sizing: border-box; /* Firefox */
        -webkit-box-sizing: border-box; /* Safari */
        border: 1px solid lightblue;
    }
```

效果如图：

![text-align:justify;列表](http://blog.codingplayboy.com/wp-content/uploads/2016/10/justify-2.png)

可以看到并没有两端对齐效果，这是因为使用text-align:justify;实现两端对齐，需要特殊的处理，我们需要在列表项最后面加一个辅助元素，样式如下：

```

   .justify-fix {
       width: 100%;
       height: 0;
       display: inline-block;
       *display: inline; /* ie */
       *zoom: 1; /* ie */
       visibility: hidden;
   }
```

比如，我们列表末尾加一个辅助li元素：

```

    <div class="wrap">
        <h3>justify两端对齐列表-v1（vertical-align: baseline;默认值）</h3>
        <ul class="list">
            <li class="item"><img src="http://blog.codingplayboy.com/wp-content/uploads/2016/06/cropped-profile-180x180.jpg" /><p>1</p></li>
            <li class="item"><img src="http://blog.codingplayboy.com/wp-content/uploads/2016/06/cropped-profile-180x180.jpg" /><p>2</p></li>
            <li class="item"><img src="http://blog.codingplayboy.com/wp-content/uploads/2016/06/cropped-profile-180x180.jpg" /><p>3</p></li>
            <li class="item"><img src="http://blog.codingplayboy.com/wp-content/uploads/2016/06/cropped-profile-180x180.jpg" /><p>4</p><p>4</p></li>
            <li class="justify-fix"></li>
        </ul>
    </div>
```
 
可以看到效果：

![justify下两端对齐](http://blog.codingplayboy.com/wp-content/uploads/2016/10/justify-3.png) 

**我们看到，两端对齐效果是有了，可是，也有问题，纵向轴线没有对齐，这是因为vertical-align属性默认是baseline值，效果是子元素的基线与父元素的基线对齐。而一个容器元素的基线是指其包含的最后一个行内元素的基线。**

于是我们可以看到实际效果是各个列表项的最后一个行内元素1，2，3，5的基线对齐。
那么该怎么办呢，我们只需要设置列表vertical-align非baseline就可以完美实现效果。

如：

```

    <div class="wrap">
        <h3>justify两端对齐列表-v2（使用vertical-align: top/bottom/middle;）</h3>
        <ul class="list">
            <li class="item align-top"><img src="http://blog.codingplayboy.com/wp-content/uploads/2016/06/cropped-profile-180x180.jpg" /><p>1</p></li>
            <li class="item align-top"><img src="http://blog.codingplayboy.com/wp-content/uploads/2016/06/cropped-profile-180x180.jpg" /><p>2</p></li>
            <li class="item align-top"><img src="http://blog.codingplayboy.com/wp-content/uploads/2016/06/cropped-profile-180x180.jpg" /><p>3</p></li>
            <li class="item align-top"><img src="http://blog.codingplayboy.com/wp-content/uploads/2016/06/cropped-profile-180x180.jpg" /><p>4</p><p>5</p></li>
            <li class="justify-fix"></li>
        </ul>
    </div>
```

样式：

```

    .align-top {
        vertical-align: top;// bottom/middle
    }
```

效果如图：

![justify下两端对齐](http://blog.codingplayboy.com/wp-content/uploads/2016/10/justify-4.png)
 

实现两端对齐的方式还有多种多样，欢迎诸位一起交流学习，本次关于两端对齐布局总结就结束了。