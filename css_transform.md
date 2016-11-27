# CSS3 transform

之前，我们介绍过[CSS3的transition过渡效果](http://blog.codingplayboy.com/2016/07/03/css3%E4%B9%8Btransition/)和[CSS3的animation动画](http://blog.codingplayboy.com/2016/11/27/css3_animation/)，本篇深入介绍CSS3 transform，即变换。

## 定义和用法

CSS transform属性向元素应用 2D 或 3D 转换，其后紧跟一个变换函数支持我们修改CSS视觉格式化模型中的元素坐标，通过声明变换函数，允许移动（转换），旋转，缩放，倾斜元素。

### 语法

transform属性CSS语法如下：

```

	transform: none | transform-functions;
```

其JavaScript语法如下：

```

	object.style.transform="rotate(17deg)";
	object.style.webkitTransform = “rotate(17deg)”；
```

## 变换函数（ transform function）

在DOM计算完后，可以使用transform属性操作DOM的渲染方式，其后紧跟一个变换函数，指明DOM的变换方式。主要的变换函数如下：

值|	描述|
------|-----|
none|	定义不进行转换。|
matrix(n,n,n,n,n,n)|	定义 2D 转换，使用六个值的矩阵。|
matrix3d(n,n,n,n,n,n,n,n,n,n,n,n,n,n,n,n)|	定义 3D 转换，使用 16 个值的 4x4 矩阵。|	
translate(x,y)	|定义 2D 移动转换。|
translate3d(x,y,z)|	定义 3D 移动转换。|	
translateX(x)|	定义X轴转换。	|
translateY(y)|	定义Y 轴转换。	|
translateZ(z)|	定义Z 轴 3D 转换。	|
scale(x,y)|	定义 2D 缩放转换。|
scale3d(x,y,z)|	定义 3D 缩放转换。|	
scaleX(x)|	通过设置 X 轴的值来定义缩放转换。|
scaleY(y)|	通过设置 Y 轴的值来定义缩放转换。|
scaleZ(z)|	通过设置 Z 轴的值来定义 3D 缩放转换。|	
rotate(angle)|	定义 2D 旋转，在参数中规定角度。|
rotate3d(x,y,z,angle)|	定义 3D 旋转。|
rotateX(angle)|	定义沿着 X 轴的 3D 旋转。|
rotateY(angle)|	定义沿着 Y 轴的 3D 旋转。|
rotateZ(angle)|	定义沿着 Z 轴的 3D 旋转。|
skew(x-angle,y-angle)|	定义沿着 X 和 Y 轴的 2D 倾斜转换。|
skewX(angle)|	定义沿着 X 轴的 2D 倾斜变换。|
skewY(angle)|	定义沿着 Y 轴的 2D 倾斜变换。|
perspective(n)|	为 3D 转换元素定义透视视图。|

## 实例

如下html结构：

```

	<div class="car"></div>
```

样式如下：

```

	.car {
		position: fixed;
		top: 10px;
		left: 10px;
		z-index: 999;
		width: 60px;
		height: 60px;
		background-color: red;
		border: 1px solid #ccc;
		border-radius: 30px;
		-webkit-transform: translate(100px, 100px);
		transform: translate(100px, 100px);
	}
```

## 硬件加速

当同时使用渐变，变换等多种图形效果时，我们会发现同一个动画在IOS和安卓上效果流畅度差别很大，这是因为IOS设备都内置了图形加速硬件。GPU，Graphics Processing Unit图形处理器，其作用主要就是优化动画及合成，开启GPU加速，可以使得动画更加流畅，在安卓上，2D变换不会开启GPU加速，其动画不在GPU上渲染，而3D变换则会开启GPU加速，使用GPU渲染图形和动画，比如使用translate3d()，而不是translate()变换函数就可以开启GPU加速，使得动画更流畅，如：

```

	transform: translate3d(100px, 100px, 0);
```

### 硬件加速限制

当使用GPU加速动画时，并不是把原生DOM传递给GPU，它生成一个元素图像，把该图像发送到GPU，GPU将图像应用为多边形纹理贴图代表元素，GPU可以流畅快速的对这些多边形进行旋转，缩放，转换，倾斜等变换，但是需要明白，由于只是传递元素图像到GPU进行处理，所以它并不能重新渲染内容。

### 2D变换与3D变换

我们知道，3D变换是会开启GPU加速，3D变换动画会更流畅，但是2D变换与3D变换还有其他差别，先看一个实例-[CSS3 2D变换与3D变换](http://demo.codingplayboy.com/demo/smallcase/htc/css3_transform_blur.html)，效果如图：

![CSS3 2D变换与3D变换对比](http://blog.codingplayboy.com/wp-content/uploads/2016/11/transform_blur.png)

如上图可见，第一个div内背景图比第二个背景图更清楚，因为第一个背景图div是使用了2D变换放大了3倍：```scale(3, 3)```,2D变换不是处理元素图像，可以比较好的还原元素质量，而第二个背景图div使用了3D变换放大了3倍：```scale3d(3, 3, 1)```，3D变换交给GPU，处理了元素图像，它只能放大该图像，元素清晰度自然就下降了。既然知道了问题何在，自然就可以对症下药：我们可以在动画或变换结束时，将元素3D变换转换成2D变换。

### 3D变换模糊状态净化

如上3D变换会导致元素变模糊，前面已经深入探讨了原因，解决办法就是在动画或变换结束时，将元素3D变换转换成2D变换，如下主要代码：

```

	;(function() {
		var TRANSFORM = 'transform';
		var TRANSITION = 'transition';
		var TRANSITIONEND = 'transitionend';
		var transform_pro = 'transform';
		var after = document.querySelector('.after');

		//事件注册兼容
		window.EventHandler = function () {
	        if (window.addEventListener) {
	            EventHandler = function(ele, type, callback, bubble) {
	                return ele.addEventListener(type, callback, bubble);
	            }
	        } else if (window.attachEvent)  {
	            EventHandler = function(ele, type, callback, bubble) {
	                return ele.attachEvent('on' + type, callback);
	            }
	        }else {
	        	EventHandler = function(ele, type, callback, bubble) {
	            	ele['on' + type] = errorReport;
	        	}
	        }
	    };

		if (typeof document.body.style.webkitTransform === 'string') {
			TRANSFORM = 'webkitTransform';
			transform_pro = '-webkit-transform';
		}
		if (typeof document.body.style.webkitTransition === 'string') {
			TRANSITION = 'webkitTransition';
		}

		EventHandler(after, 'webkitTransitionEnd', function() {
			after.style[TRANSFORM] = 'scale(3, 3)';
		});
		EventHandler(after, 'transitionend', function() {
			after.style[TRANSFORM] = 'scale(3, 3)';
		});
		setTimeout(function() {
			after.style[TRANSITION] = transform_pro + ' 1s ease-in';
			after.style[TRANSFORM] = 'scale3d(3, 3, 1)';
		}, 1000);
	})();
```

查看实例-[CSS3 3D变换模糊状态净化](http://demo.codingplayboy.com/demo/smallcase/htc/css3_transform_blur_repair.html)

上面实例中，第二个div元素逐渐放大然后变得清晰；我们为第二个div元素绑定transition过渡效果，实现3D变换，然后在transitionend事件回调中将3d变换转成2D变换，元素清晰度得到净化，变得清晰。

## 小球弹动

还记得之前使用CSS3 transition-[transition实例](http://demo.codingplayboy.com/demo/smallcase/htc/css3_transition_ball.html)和animation-[animation实例](http://demo.codingplayboy.com/demo/smallcase/htc/css3_animation_ball.html)实现过小球弹动动画，之前都是使用的position定位实现的弹动动画，现在我们还可以使用transform方式实现。

html结构如下：

```

	<div class="ball"></div>
```

### 创建动画和变换

使用@keyframes规则创建动画和关键帧，并在关键帧内使用transform变换实现小球移动状态：

```

	@-webkit-keyframes bounce {
		0% {
			-webkit-transform: translate(0, 30px);
			-webkit-animation-timing-function: cubic-bezier(1, 0, 0.96, 0.91);
		}
		50% {
			-webkit-transform: translate(0, 300px);
			-webkit-animation-timing-function: cubic-bezier(0, 0.27, 0.32, 1);
		}
		100% {
			-webkit-transform: translate(0, 30px);
			-webkit-animation-timing-function: cubic-bezier(0, 0.27, 0.32, 1);
		}
	}
	@keyframes bounce {
		0% {
			transform: translate(0, 30px);
			animation-timing-function: cubic-bezier(1, 0, 0.96, 0.91);
		}
		50% {
			transform: translate(0, 300px);
			animation-timing-function: cubic-bezier(0, 0.27, 0.32, 1);
		}
		100% {
			transform: translate(0, 30px);
			animation-timing-function: cubic-bezier(0, 0.27, 0.32, 1);
		}
	}
```

### 绑定动画

将上面定义的动画绑定到特定选择器：

```

	.ball {
		position: fixed;
		top: 10px;
		left: 10px;
		z-index: 999;
		width: 60px;
		height: 60px;
		background-color: red;
		border: 1px solid #ccc;
		border-radius: 30px;
		-webkit-animation: bounce 3s infinite;
		animation: bounce 3s infinite;
	}
```

效果查看实例-[CSS3 transform弹动小球](http://demo.codingplayboy.com/demo/smallcase/htc/css3_transform_bounce.html)

## 