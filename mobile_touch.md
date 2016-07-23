# 移动开发之轻触与单击事件

在桌面浏览器，我们点击一个链接或绑定了click单击事件的元素时，需要先将鼠标移动到元素上，再单击；这个click事件有一个鼠标按下和松开的动作。而在移动设备触摸界面上，我们只需要轻触一个链接或绑定了轻触事件的元素，即可触发事件。

## 单击与双击

我们知道在DOM事件里有单击（click）和双击（dblclick）两种点击事件，一般情况下，双击事件触发时也会触发两次单击事件：

```

	var $btn = $('.btn');
	var $txt = $('.txt');

	function clickFunc(event) {
		$txt.append('<p>普通： 触发了一次单击click</p>');
	}
	function dblclickFunc(event) {
		$txt.append('<p>普通： 触发了一次双击dblclick</p>');
	}

	//普通的单击双击
	$btn.on('click', clickFunc);
	$btn.on('dblclick', dblclickFunc);
```
双击后触发事件输出如图：

![普通双击事件](http://blog-resource.bj.bcebos.com/photos/2016/07/dblclick.png)

然而这种情形并不是我们期望的，我们期望的是单击与双击事件独立触发，这需要我们在代码中优化：

```

	var $btn2 = $('.btn2');
	var $txt = $('.txt');

	function clickFunc2(event) {
		if (!timer) {
			timer = setTimeout(function() {
				$txt.append('<p class="important-txt">优化后： 触发了一次单击click2</p>');
				clearTimeout(timer);
				timer = null;
			}, 400);
		}
	}
	function dblclickFunc2(event) {
		clearTimeout(timer);
		timer = null;
		$txt.append('<p class="important-txt">优化后： 触发了一次双击dblclick2</p>');
	}

	//优化后的单击双击
	$btn2.on('click', clickFunc2);
	$btn2.on('dblclick', dblclickFunc2);
```

优化过的双击事件输出如图：

![优化过的双击事件](http://blog-resource.bj.bcebos.com/photos/2016/07/dblclick2.png)

[点此前往本章关于单击与双击的线上测试地址](http://demo.codingplayboy.com/demo/smallcase/htc/click_dblclick.html)

## 触摸事件

在上一节，我们学习了双击会触发单击事件，在移动设备触摸界面，大部分浏览器默认，连续两次轻触会缩放页面。在触摸界面，轻触某链接或绑定了click事件的元素之后，为了确保该轻触不是某一个连续两次轻触中的第一次，浏览器等待大约300ms后才会触发click事件。接下来，我们需要详细了解移动浏览器的触摸事件。

### 4种触摸类型事件

移动浏览器触摸事件有四种：touchstart,touchmove,touchend,touchcancel。

|事件名称    | 说明 | 事件对象 |
|-----------|---------|--------|
|touchstart |触摸开始  |touches |
|touchmove  |触摸点移动 |touches|
|touchend   |触摸结束  |touches |
|touchcancel|触摸取消  |无 |

就像桌面事件一样，触发DOM事件时，回调函数会传入一个Event事件对象，在移动端触发轻触事件（touchcancel除外），由于触摸设备支持多点触控，回调函数会传入一个触摸事件对象数组 ，一个触摸对象与一个触摸点对应。下面简单列出了触摸对象的主要属性及说明。

|属性    | 说明 | 读写  |
|-----------|----------------|-------|
|identifier |触摸对象的唯一标识，触摸点未离开屏幕，值不变|只读|
|target     |触摸开始时，触摸点接触的DOM元素，值不会随触摸点移动而改变|只读|
|screenX    |触摸点X坐标，值为到屏幕左侧边缘的距离|只读|
|screenY    |触摸点Y坐标，值为到屏幕顶部边缘短距离|只读
|clientX    |相对于浏览器窗口左侧的X坐标，值为到浏览器窗口左侧边缘的距离|只读|
|clientY    |相对于浏览器窗口顶部的Y坐标，值为到浏览器窗口顶部边缘的距离|只读|
|pageX      |相对于页面文档左侧的X坐标，其值包括文档水平滚动过的距离|只读|
|pageY      |相对于页面文档顶部的Y坐标，其值包括文档垂直滚动过的距离|只读|
|radiusX    |与触摸接触区域最吻合的椭圆的X半径|只读|
|radiusY    |与触摸接触区域最吻合的椭圆的Y半径|只读|
|force      |触摸时施加的压力，值为0.0到1.0之间|只读

### IE10的指针事件

相对于常规的touch触摸事件，在IE10支持其特有的pointer指针事件，其可以由手指，触控笔或鼠标触发：

|事件名称    | 说明 | 事件对象 |
|-------------|-------------|------|
|MSPointerDown |触摸开始    |MSPointerEvent|
|MSPointerMove |触摸点移动  |MSPointerEvent|
|MSPointerUp   |触摸结束   |MSPointerEvent|
|MSPointerOver |触摸点移动到元素上|MSPointerEvent|
|MSPointerOut  |触摸点离开元素|MSPointerEvent|


指针事件对象常用属性：

|属性名称 | 说明 |
|-----------|-------|
|pointerId  |指针的唯一标识|
|pointerType|标识指针事件来源于鼠标，手指或是触控笔，值为一个整数|
|clientX\|Y  |参见touch事件|
|pageX\|Y    |参见touch事件|

## 实践

### touch实例v0.1

先来一个实例：[点此进入touch事件线上实例](http://demo.codingplayboy.com/demo/mobile/mobile_touch.html)

js代码如下:

```

	var togglePicBtn = document.getElementById('toggle-touch');
	function togglePicture() {
		var displayState = picWrapper.style.display;
		if (displayState === 'none' || !displayState) {
			picWrapper.style.display = 'block';
		}else {
			picWrapper.style.display = 'none';
		}
	}

	togglePicBtn.addEventListener('touchstart', function(event) {
		event.preventDefault();
		event.target.className = 'active button';
		togglePicture();
	});

	togglePicBtn.addEventListener('touchend', function(event) {
		event.preventDefault();
		event.target.className = 'button';
	});
```

### touch实例v0.2

实例v0.1是能比较好的在标准浏览器上运行的，但是IE10和桌面都是无法运行的，我们有必要封装不同浏览器对不同事件的支持以及在不支持触摸或指针事件时降级使用更标准的事件。

参考jQuey或zepto类库对DOM事件的封装，我们可以使用门面模式封装不同的事件处理，为用户提供统一，简单的API。

```
	
	//自执行函数
	;(function(exports) {
		
		var TapEventPro;
		var TOUCHSTART;
		var TOUCHEND;
		
		//标准touch触摸事件
		if (typeof window.ontouchstart !== 'undefined') {
			TOUCHSTART = 'touchstart';
			TOUCHEND = 'touchend';
		}else if (typeof window.onmspointerdowm != 'undefined') {
			TOUCHSTART = 'MSPointerDown';
			TOUCHEND = 'MSPointerUp';
		}else {
			TOUCHSTART = 'mousedown';
			TOUCHEND = 'mouseup';
		}

		function TapEvent(ele) {
			this.ele = ele;
		}

		TapEventPro = TapEvent.prototype;
		TapEventPro.on = function(eventType, callback) {
			if (eventType === 'tap') {
				this.ele.addEventListener(TOUCHSTART, callback);
			}else if (eventType === 'tapend') {
				this.ele.addEventListener(TOUCHEND, callback);
			}else {
				this.ele.addEventListener(eventType, callback);
			}
			return this;
		};
	})(window);
```