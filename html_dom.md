# HTML DOM(一)
DOM(Document Object Model)，定义了表示和操作HTML或XML文档的标准方法。DOM是在浏览器中加载的一个文档模型并且把文档表达为一棵节点树，节点树中每个节点表示文档的一部分。

DOM是网络中使用最多的一系列API，因为它允许在浏览器中运行的代码访问并操作文档中的每一个节点。可以创建、移动、修改节点。可以给节点注册事件并触发已注册事件，执行事件处理程序。

## DOM节点层次

DOM可以将HTML或XML文档表示成一个多节点的节点树。

如下HTML文档：

```
    <!doctype html>
    <html>
        <head>
            <title>DOM</title>
        </head>
        <body
            <h1>DOM</h1>
            <a href="#">Document Object Model</a>
        </body>
    </html>
```

![DOM节点树](http://blog-resource.bj.bcebos.com/photos/2016_01/dom.png)

文档节点是每个文档的根节点，上例中文档节点的一个子节点<html\>元素，是文档的最外层元素，即文档元素。
文档中的每一段标记都可以通过树中节点表示：文档类型通过文档类型节点表示，HTML元素通过元素节点表示，特性通过特性节点表示，注释通过注释节点表示，所有类型节点都继承自基类型：Node类型。

*在HTML文档中，文档元素始终是<html\>元素。*

### Node类型
DOM1级定义了一个Node接口，该接口作为Node类型实现的，JavaScript中的所有节点类型都继承自Node类型。

#### nodeType

Node类型中定义了12个数值常量，文档节点类型均在其中，每个节点nodeType属性值均在其中，如下HTML文档节点常见类型：

常量                  | 描述
----------------------| -------------------
Node.ElEMENT_NODE(1)  |元素节点
Node.ATTRIBUTE_NODE(2)|属性节点
Node.TEXT_NODE(3)     |文本节点
Node.COMMENT_NODE(8)  |注释节点
Node.DOCUMENT_NODE(9) |文档节点
Node.DOCUMENT_TYPE_NODE(10） |文档类型节点
Node.DOCUMENT_FRAGMENT_NODE(11)  |文档片段节点

通过比较这些常量，可以很容易确定节点类型：

```

    var node = document.getElementById('myNode');
    if (node.nodeType == 1) {
        console.log('元素节点');
    }
```

#### nodeName与nodeValue

每个节点都有nodeName和nodeValue属性，保存着节点相关信息，比如元素节点，其nodeName属性值为元素标签名，nodeValue为null。

#### 节点关系

节点树中各节点之间存在着层级关系，主要表现为：父(parent)、子(child)、同胞(sibling)关系。

- parentNode

每个节点都有一个parentNode属性，该属性指向该节点在文档树中的父节点，如下：

```

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>DOM</title>
    </head>
    <body>
    	<div id="dParent">
    		<p id="pChild">childNodes</p>
    	</div>
    	<script>
    		window.onload = function() {
    			var p = document.getElementById('pChild');
    			var d = document.getElementById('dParent');
    			console.log(p.parentNode === d); //输出true
    		}
    	</script>
    </body>
    </html>
```

- childNodes

每个节点也有一个childNodes属性，其值是一个NodeList对象：

> NodeList是一种类数组对象，保存着一组有序的节点，可以通过位置访问这些节点。

```

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
    	<title>DOM</title>
    </head>
    <body>
    	<div id="dParent">
    		<p id="pChild">childNodes one</p>
            <!--comment -->
    		<p id="pChild2">childNodes two</p>
    		<p id="pChild3">childNodes three</p>
    	</div>
    	<script>
    		window.onload = function() {
    			var p = document.getElementById('pChild');
    			var d = document.getElementById('dParent');
    			console.log(d.childNodes.length);//输出9
    			console.log(d.childNodes[1] === p); //输出true
    		}
    	</script>
    </body>
    </html>
```
*注：IE8-环境下，d.childNodes.length输出4。*

childNodes有length属性，在Chrome/FireFox/IE9+环境下，其值包括父节点下的所有文本节点、元素节点和注释节点，此例中有五个文本节点，三个元素节点，一个注释节点；元素节点和注释节点读者一眼就能看出，那么那五个文本节点来自何处呢？我们试着把各元素节点和注释节点间的空格和换行删掉，就会发现文本节点少了，原来这里的五个文本节点来自于各p标签以及注释节点间的换行。

*注父节点的firstChild等于node.childNodes[0]，lastChild等于node.childNodes[node.childNodes.length - 1]。*

- previousSibling和nextSibling

previousSibling和nextSibling属性分别表示当前节点的相邻的前一个同胞节点，相邻的后一个同胞节点。同胞节点具有相同父节点。

- ownerDocument

该属性指向整个文档的文档节点，即文档根节点，如：

```

    var p = document.getElementById('pChild');
    console.log(p.ownerDocument);//输出#document文档节点
```

#### 操作节点
本节介绍操作节点的一些常用方法，如下：

方法            |描述
----------------|--------------------
appendChild     |向某节点的childNodes列表末尾新增一个节点，返回新增节点。
insertBefore    |在某节点的childNodes列表某特定位置插入节点，返回被插入节点
replaceChild    |替换文档中某节点的子节点，返回要替换的节点。
removeChild     |移除文档中某节点子节点，返回该移除节点。
cloneNode       |复制某节点，返回该节点的一个副本。

- appendChild

```

    <script>
    	window.onload = function() {
            var p = document.getElementById('pChild');
            var d = document.getElementById('dParent');
            var node = document.createElement('p');
            node.textContent = 'new Node';
            var reNode1 = d.appendChild(node); 
            console.log(reNode1 === node); //输出true
            var reNode2 = d.appendChild(p);
            console.log(reNode2 === p); //输出true
		};
    </script>
```
若appendChild接收的节点参数是新创建的节点，则只在父节点的childNodes列表末尾新增一个节点；若参数是文档中某节点，则就是将该节点从原来位置移动到childNodes列表最后的位置。

- insertBefore

此方法接受两个参数：要插入的节点，参照节点；将待插入节点插入到参照节点前面，即变成参照节点的previousSibling属性指向的节点；若参照节点为null，则此方法等同于执行appendChild()。

```
    
    <script>
        window.onload = function() {
            var p = document.getElementById('pChild');
            var d = document.getElementById('dParent');
            var node = document.createElement('p');
            node.textContent = 'new Node';
            var reNode1 = d.insertBefore(node, p); 
            console.log(reNode1 === node); //输出true
            var reNode2 = d.insertBefore(p, null);
            console.log(reNode2 === p); //输出true
		};
    </script>
```
若insertBefore接收的待插入节点参数是新创建的节点，则只在父节点的childNodes列表对应位置插入一个节点；若参数是文档中某节点，则就是将该节点从原来位置移动到childNodes列表的对应位置。

- replaceChild

此方法接受两个参数：要插入的节点，要替换的节点；此方法将要替换节点删除并返回。

```

    <script>
        window.onload = function() {
            var p = document.getElementById('pChild');
            var p2 = document.getElementById('pChild2');
            var d = document.getElementById('dParent');
            var node = document.createElement('p');
            node.textContent = 'new Node';
            var reNode1 = d.replaceChild(node, p); 
            console.log(reNode1 === p); //输出true
            var reNode2 = d.replaceChild(p, p2);
            console.log(reNode2 === p2); //输出true
    	};
    </script>
```
若replaceChild接收的待插入节点参数是新创建的节点，则只替换要替换的节点；若参数是文档中某节点，则就是将该节点从原来位置移动到要替换的节点对应位置。

- removeChild

此方法接受一个参数：待移除的节点；将传入的节点移除并返回。

```

    <script>
        window.onload = function() {
            var p = document.getElementById('pChild');
            var d = document.getElementById('dParent');
            var reNode1 = d.removeChild(p); 
            console.log(reNode1 === p); //输出true         
        };
    </script>
```

- cloneNode

此方法创建调用此方法的节点的一个副本，接受一个参数：true | false，true表示深复制，即复制节点及其所有子节点；false表示浅复制，即只复制该节点。

```

    <script>
        window.onload = function() {
            var d = document.getElementById('dParent');
            var reNode1 = d.cloneNode(true); 
            console.log(reNode1.childNodes.length);//输出9
            var reNode2 = d.cloneNode(false);
            console.log(reNode2.childNodes.length);//输出0
            console.log(reNode1 === d); //输出false         
        };
    </script>
```

本篇介绍了HTML DOM、节点树、文档中所有类型节点的基类型Node类型及Node节点操作、下篇将介绍DOM中更多具体的节点类型及其相关操作。