---
layout: post
title: JavaScript Notes
permalink: readme.md
description: javascript domReady
date: 2015-12-13 19:44:20 +08:00
tags: "JavaScript 笔记分享"
---

#JavaScript之Load与ready

本篇笔记要谈的就是耳熟能详的window的load事件，及其与目前流行的jQuery中的ready事件的区别，最后简易实现一个domReady函数。

##load事件

window的load事件是JavaScript中最常用的事件之一，当页面完全加载完后（包括所有图片、JavaScript文件、CSS文件等资源），才触发load事件。有两种定义load事件的方式：

-  通过JavaScript注册load事件

```
	function addHandler(elem, type, handler) {
		if (document.addEventListener) {  //非IE浏览器注册事件处理程序
			elem.addEventListener(type, handler);
		}else if (document.attachEvent) {  //IE浏览器注册事件处理程序
			elem.attachEvent('on' + type, handler);
		}else {
			elem['on' + type] = handler;
		}
	}
	addHandler(window, 'load', function(event) {
		console.log('Loaded!');
	});
```

-  通过为body元素添加onload特性

```
	...
	<body onload="alert('Loaded')">
	</body>
```

*注：页面中只能有一个基于window的load事件*

##jQuery之ready

jQuery的ready事件是在形成完整的DOM树之后便会触发，不必等到所有外部资源加载完。
其定义形式如：


```
	$(document).ready(function() {
	//DOM操作...
	})
	或
	$(function() {
	//DOM操作...
	})
```

*注：页面中只有一个基于window的load事件能执行，而$(document).ready()可以同时编写多个，并且都可以得到执行。*

##DOMContentLoaded事件

与window的load事件不同，DOMContentLoaded事件与jQuery的ready事件一样在形成完整的DOM树之后便会触发，可以为window或document添加相应事件处理程序，然而其在IE中并不支持，下文有详解。

```
	//addHandler函数与上文load事件处同
	addHandler(document, 'DOMContentLoaded', function() {
		console.log('Content Loaded!');
	});
```

##IE与文档加载

IE中以readystatechange事件提供文档或元素的加载状态相关信息，支持readystatechange事件的对象均有一个readyState属性。

> readyState可能包含下列五个值之一：
uninitialized(未初始化)：对象存在但尚未初始化。
loading(正在加载)：对象正在加载数据。
loaded(加载完毕)：对象加载数据完成。
interactive(交互)：可以操作对象，但没有完全加载。
complete(完成)：对象已经加载完毕。

##JavaScript模拟实现ready事件

此段实现借鉴自半年前于慕课网所学笔记。

```
	function myReady(fn) {
		if (document.addEventListener) {
			document.addEventListener("DOMContentLoaded", fn, false);
		}else {
			IEContentLoaded(fn);
		}
		function IEContentLoaded(fn) {
			var done = false;
			var d = window.document;
			var init = function() {
				//只能执行一次
				if (!done) {
					done = true;
					fn();
				}
			};
			(function() {
				try {
					d.documentElement.doScroll('left'); 
					//IE8及以下浏览器在文档DOM树未加载完时执行doScroll()会抛出错误跳到catch内执行
				}catch(e) {
					setTimeout(arguments.callee, 50);
					//文档DOM树未加载完，50ms后再次运行本自执行函数以探测文档DOM树是否加载完成
					return;
					//递归探测文档DOM树是否加载完成
				}
				init();
			})();
			d.onreadystatechange = function() {
				if (d.readyState== 'interactive' || d.readyState == 'complete') {
					d.onreadystatechange = null;
					//移除相应事件处理程序以免其他阶段再次执行
					init();
				}
			}
		}
	}
```

以上就是一个简易的原生实现的ready函数，其功能与jQuery的ready相似。



