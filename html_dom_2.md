# HTML DOM(二)
上篇介绍了HTML DOM、节点树、文档中所有类型节点的基类型Node类型及Node节点操作、本篇介绍DOM中更多具体的节点类型及其相关操作。

## Document类型--文档节点
每个载入浏览器的 HTML 文档都会成为 Document 对象。在浏览器中document对象是HTMLDocument的一个实例，表示整个HTML文档，而HTMLDocument又继承自Document类型。因此我们可以通过document对象来访问HTML文档中所有元素。

*注：document是window对象的一个属性，因此其是全局对象。*

在HTML DOM中,Document有以下特性：

属性          |值
--------------|---------
nodeType      |9
nodeName      |#document
nodeValue     |null
parentNode    |null
childNodes    |0或1个DocumentType，0或1个Element，0或若干个Comment节点
ownerDocument |null

### 文档节点的子节点
通过之前的学习，我们知道文档节点的唯一元素子节点（Element类型）是文档元素，即html元素，访问方式有两种：

- document.childNodes加对应索引访问
- documentElement属性始终指向HTML页面的html元素，[更多详情可参见文章漫谈document.documentElement与document.body](http://blog.codingplayboy.com/index.php/2016/01/26/html_docbody/)。

### Document对象属性
属性            |描述
----------------|---------------
body            |指向body元素，对于定义了框架集的文档，该属性引用最外层的 <frameset>。
title           |title元素中的文本，为页面的标题。
URL             |当前页面完整的URL，只读。
domain          |当前页面所在域域名，只能修改为当前域的上一级域名。
referrer        |载入当前文档的文档的 URL，只读。

```

    document.title = 'Modify Title'; //页面标题被修改
    
    //假设当前页面在blog.codingplayboy.com域下
    document.domain = 'codingplayboy.com'; //成功，域改为codingplayboy.com
    document.domain = 'blog.codingplayboy.com';/出错
```

**因为存在跨域安全限制，不同子域之间的页面不能通过JavaScript通信，可以通过将不同页面的document.domain设置为相同值，页面间就可以相互访问对方的JavaScript对象。**

### Document对象方法
方法                      |描述
--------------------------|---------------
getElementById            |获取拥有指定 id 的第一个对象的引用.
getElementsByName         |获取带有指定名称的对象集合。
getElementsByTagName      |获取带有指定标签名的对象集合。

### Document对象集合
属性                      |描述
--------------------------|---------------
anchors                   |文档中所有带name特性的a元素
forms                     |文档中所有form元素
images                    |文档中所有img元素
links                     |文档中所有带href特性的元素

## Element类型--元素节点
HTML文档中所有元素均由HTMLElement类型或HTMLElement子类型表示，HTMLElement类型继承自Element类型。
Element类型提供了对元素标签名、特性、子节点的访问，具有以下特性：

属性          |值
--------------|---------
nodeType      |1
nodeName      |该元素标签名
nodeValue     |null
parentNode    |Document或Element
childNodes    |Element或Text或Comment

```

    var d = document.getElementById('mydiv');
    console.log(d.tagName == d.nodeName);//输出true
```

### HTML 元素
HTMLElement类型相对于Element类型，其存在以下标准特性：

特性          |值
--------------|---------
id            |文档中该元素的唯一标识符
title         |该元素主题信息
dir           |元素语言的方向，dir="ltr"表示从左到右，dir="rtl"表示从右到左
className     |与元素的class特性对应
lang          |元素内容的语言代码

```

    <div id="mydiv" class="fl" title="Html Element" lang="en" dir="ltr"></div>
    <script>
        var d = document.getElementById('mydiv');
        console.log(d.className); //输出fl
    </script>
```
### 操作元素特性
操作特性的DOM方法主要有三个：getAttribute()、setAttribute()、removeAttribute()
