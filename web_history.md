# 深入学习History对象管理浏览器会话历史

History对象允许我们操作浏览器会话历史，即加载当前页面的标签页窗口或frame窗口的访问历史。之前有同学咨询我如何实现拦截用户跳转页面并强制用户返回首页后重新请求页面，于是有了本篇博客的主题，本篇深入介绍浏览器会话历史的操作，在最后对比加载页面的几种不同方式，并提供一个实例給读者把玩。

## 属性

- History.length

	只读的，其值为一个整数，标志包括当前页面在内的会话历史中的记录数量，比如我们通常打开一个空白窗口，length为0，再访问一个页面，其length变为1。
	
- History.scrollRestoration

	允许web应用在会话历史导航时显式地设置默认滚动复原，其值为auto或manual。

- History.state

	只读，返回代表会话历史堆栈顶部记录的任意可序列化类型数据值，我们可以以此来区别不同会话历史纪录。

## 方法

- History.back()

	返回会话历史记录中的上一个页面，等价于window.history.go(-1)和点击浏览器的后退按钮。

- History.forward()

	进入会话历史记录中的下一个页面，等价于window.history.go(1)和点击浏览器的前进按钮。

- History.go()

	加载会话历史记录中的某一个页面，通过该页面与当前页面在会话历史中的相对位置定位，如，-1代表当前页面的上一个记录，1代表当前页面的下一个页面。若不传参数或传入0，则会重新加载当前页面；若参数超出当前会话历史纪录数，则不进行操作。

- History pushState()

 	在会话历史堆栈顶部插入一条记录，参数包括，任意可序列化的object对象数据（可选），页面标题（可选），页面URL（非空）。

	**目前，Firefox忽略页面标题参数。**

- History.replaceState()

 	更新会话历史堆栈顶部记录信息，包括特定的任意可序列化的object对象数据（可选），页面标题（可选），页面URL。

**值得注意的是，无论是replaceState()方法还是pushState()方法，其更新或添加会话历史记录后，改变的只是浏览器关于当前页面的标题和URL的记录情况，并不会刷新或改变页面展示。**

## window.history

window的history是只读属性，该属性返回History对象的一个引用，支持我们操作浏览器会话历史记录。

出于安全考虑，History对象不允许我们通过JavaScript代码访问其他会话历史记录中其他页面的URL，但是它允许我们在不同页面间进行导航。

### onpopstate事件

HTML5中，提供history.pushState()和history.replaceState()方法，支持我们添加或更新会话历史记录，另外还提供window.onpopstate事件支持我们对该操作进行监听。

### pushState()

pushState()方法接收三个参数，一个state对象，一个页面标题，一个URL:

1. 状态对象：
	1. 存储新添会话历史记录的状态信息对象，每次访问该条会话时，都会触发popstate事件，并且事件回调函数会接收一个参数，值为该事件对象的复制副本；
	1. 状态对象可以是任何可序列化的数据，浏览器将状态对象存储在用户的磁盘以便用户再次重启浏览器时能恢复数据；
	1. 一个状态对象序列化后的最大长度是640K，如果传递数据过大，则会抛出异常。

1. 页面标题：
	1. 目前 ，该参数值会被忽略，暂不被使用，可以传入空字符串。

1. 页面URL：
	1. 此参数声明新添会话记录的入口URL；
	1.  在调用pushState()方法后，浏览器不会加载URL指向的页面（在重启浏览器后或许会加载新页面 ），我们可以在popstate事件回调中处理页面是否加载；
	1.  此URL必须与当前页面URL同源,，否则会抛异常；其值可以是绝对地址，也可以是相对地址，相对地址会被基于当前页面URL解析 得到绝对地址；若其值为空，则默认是当前页面URL。	 

pushState()方法可以改变URL地址栏，在会话历史堆栈顶部插入一条新会话记录，如：

 
```

	var stateObj = { foo: "bar" };
	history.pushState(stateObj, "page 2", "bar.html");
```

假如当前访问页面http://blog.codingplayboy.com，则执行以上js代码后，浏览器地址栏变为http://blog.codingplayboy.com/bar.html，页面展示并没有变化，随后，

- 若我们点击浏览器的后退按钮，页面也不会变化，只是浏览器的地址栏URL变换为之前的http://blog.codingplayboy.com；
- 若我们点击跳转到http://blog.codingplayboy.com/about.html,然后再点击浏览器的后退按钮，则页面会回退到http://blog.codingplayboy.com/bar.html页面，且在该页面触发popstate事件，会向事件回调传入一个之前定义的stateObj的复制值参数；
- 我们可以在popstate事件回调中执行我们的任务，但是必须知道的是，只有当当前页面DOM加载完成（即DOMContentLoaded事件发生）后才会触发popstate事件。
- pushState()方法不会触发hashchange事件，即使URL的hash片段值改变。

### replaceState()

与history.pushState()方法相比，history.replaceState()方法不创建新的会话历史，而是更新当前会话历史记录，如更新当前会话记录的状态对象或URL。

### popstate事件

每次会话记录变换激活都会在window上触发popstate事件，如果激活的会话记录是通过replaceState()更新的或使用pushState()方法创建的，popstate事件对象的state属性值就是该会话记录状态对象的一个副本。

## location.reload()与location.replace()小结

我们知道location.reload()和location.replace()方法还有直接设置location值，都可以重新加载页面，但是这三种方式也是有区别的：

### reload()

- 语法
	
	location.reload(beForceGet)
	
- 参数
    
    	beForceGet，可选，值为true或false；默认为false，表示是否从客户端缓存读取当前页；为true时，则从服务端重新请求页面（相当于F5刷新和history.go(0)方法）。
  
  
### replace()

- 语法

	location.replace(url)
	
- 参数

	一个相对或绝对URL，使用相对于当前页URL解析后的URL替换当前会话记录的URL，效果与使用history.replaceState()方法修改URL相同；
	
- 对比reload()

	location.reload()会取客户端的缓存页面，但是location.replace(url)总是重新请求加载url指向的页面。
 
### location赋值

- 语法
 
	location.href = url;
	或
	location.assign(url);

- 说明

	可以为location直接设置一个URL值，该值等效于使用pushState()修改URL，会创建一条新会话记录。

 

## 拦截用户返回页面及强制请求新页面实例

[点此传送到实例访问地址](http://demo.codingplayboy.com/demo/smallcase/htc/history/index.html)

首先，我们进入首页index.html，并点击任意跳转，跳转到第二页a.html（当然在实际应用中可以是任意页面），然后点击返回，我们会发现，并没有返回到我们访问的首页，而是进入了我们设置的拦截页，具体如何实现的呢，因为我们在第二页中编写JavaScript代码实现：

```

	;(function() {
		window.onpopstate = function(event) {
			console.log(event.state);
			location.replace('replace.html');
		};
		history.pushState({name: '惊鸿'}, '', 'a.html?history=1');
	})();
```

我们调用pushState()方法创建了一条新会话记录（该会话URL为a.html?history=1，仔细看浏览器地址栏变URL变成了a.html?history=1,），并绑定了popstate事件回调，当浏览器返回时，会退回到上一条会话记录，即a.html会话，然后触发popstate事件，在事件回调函数中，我们调用location.replace('replace.html')将a.html页面跳转至replace.html（可以是任意同源页面），这就实现了拦截用户跳转；随后，再次点击返回，会返回到我们访问的第一个页面，我们查看NetWork请求会发现不同于之前返回的页面（请求状态码为304），其状态码是200，说明是一次新的请求，这是因为在replace.html页面中，加了如下代码：

```

	;(function() {
		window.onpopstate= function(event) {
			console.log(event.state);
			document.location.replace(location.href);
		};
	})();	
```

我们在popstate事件回调中，使用```location.replace()```方法强制刷新了当前页面；当我们在拦截页点击返回时，会回退到第一页会话，URL为index.html，然后触发popstate事件，执行```document.location.replace(location.href);```刷新页面。
 
 
pushState()和replaceState()能做的比我们想象的要多，本文比较详细的对其进行了介绍，有兴趣的同学可以参考MDN或玩w3c，进行更深入的学习，也可以搜索PJAX，即PushState和Ajax，同时使用这两个工具，可以极大加快网站响应速度。
 