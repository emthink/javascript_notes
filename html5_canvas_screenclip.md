# HTML5 canvas截图

上周遇到一个问题，是前端如何实现页面截图，第一反应就是响起了canvas，经过试验和总结，发现使用canvas可以实现屏幕截图，但是其实用性尚有局限，现进行阐述。

## 前言

要使用canvas实现页面截图，首先，我们需要在canvas内描画DOM内容，先来看个实例：

[canvas实现页面截图](http://demo.codingplayboy.com/demo/canvas_clip/index.html)

## 在canvas内描画DOM内容

目前，是无法在canvas内直接描画DOM内容，而是必须使用SVG图片包含需要渲染的内容，在SVG中使用```<foreignObject>```元素包含HTML结构，然后调用canvas API中drawImage()方法将SVG图片描画到canvas内。

### 创建SVG图片

首先创建一个SVG图片，我们需要创建一个包含SVG相关的XML信息（该SVG必须是有效的XML），然后构建一个Blob对象：

```

	var data = '<svg xmlns="http://www.w3.org/2000/svg" width="100%" height="100%">' +
		       '<foreignObject width="100%" height="100%">' +
		       '<div xmlns="http://www.w3.org/1999/xhtml" style="font-size:40px">' +
                 '<p>测试</p>' +
               '</div>' +
		       '</foreignObject>' +
		   '</svg>';
	var DOMURL = window.URL || window.webkitURL || window;
	var img = new Image();
	var svg = new Blob([data], {type: 'image/svg+xml'});
	var url = DOMURL.createObjectURL(svg);

	img.onload = function () {
		//canvas.width = window.innerWidth;
		//canvas.height = window.innerHeight;
		//ctx.drawImage(img, 0, 0);
		//DOMURL.revokeObjectURL(url); // 回收URL
		//canvas.style.display = 'block';
	}
	img.src = url;

```

如上，首先创建一个svg字符串，该字符串中包含```<foreignObject>```元素，该元素内容即为需要canvas渲染的DOM内容；然后使用Blob构造函数创建一个MIME类型为image/svg+xml的Blob实例；随后调用window的URL对象的createObjectURL()方法创建一个HTML文档内联URL对象实例；然后通过```new Image()```创建了一个HTML```<img>```元素，并将URL对象数据添加至该图像（设置其srcs属性）。

## canvas绘图

将上文创建的HTML```<img>```元素内容绘制到canvas内：

```

	var canvas = document.getElementById('canvas');
	var ctx = canvas.getContext('2d');

	img.onload = function () {
		canvas.width = window.innerWidth; // 设置canvas宽高
		canvas.height = window.innerHeight;
		ctx.drawImage(img, 0, 0); // 绘制图片
		DOMURL.revokeObjectURL(url); // 回收URL
		canvas.style.display = 'block';
	};
```

如上使用canvas API的drawImage()方法将img图片绘制至canvas。

## 获取DOM内容

上文提到，svg中```<foreignObject>```元素内包含的是需要渲染的DOM内容，DOM内容的根元素必须设置xmlns属性，如：

```

	xmlns="http://www.w3.org/1999/xhtml"
```

如下是获取当前文档HTML DOM内容：

```

	var _html = document.documentElement.innerHTML;
	var doc = document.implementation.createHTMLDocument("");

	// You must manually set the xmlns if you intend to immediately serialize 
	// the HTML document to a string as opposed to appending it to a 
	// <foreignObject> in the DOM
	doc.documentElement.setAttribute("xmlns", doc.documentElement.namespaceURI);
	doc.documentElement.innerHTML = _html;

	// Get well-formed markup
	var html = (new XMLSerializer).serializeToString(doc.documentElement);
```

## 效果

在这个例子中，我们首先隐藏了canvas元素，随后绘制好DOM内容，然后显示canvas查看截图：

```

	canvas.style.display = 'block';
```

效果如图：

![canvas页面截图](http://blog.codingplayboy.com/wp-content/uploads/2016/11/canvas_screen_clip.png)

## 限制

- 我们发现通过外链引入的CSS样式是无法通过canvas截图复制的，只有内联或行内样式才能复制显示在canvas内。
- 由于安全原因，无法直接下载保存图片，但是可以通过别的方式获取图片数据，下篇详细介绍。

本篇相关完整代码查看[html5 canvas页面截图https://github.com/codingplayboy/web_demo/blob/master/html5/canvas_clip/canvasScreenClip.html](https://github.com/codingplayboy/web_demo/blob/master/html5/canvas_clip/canvasScreenClip.html)，之后将继续介绍如何将该图片保存或传送至服务器。


