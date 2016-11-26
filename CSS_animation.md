# CSS3 animation

在之前，我们介绍过CSS3 transition过渡，其中实现了一个[小球弹动动画](http://demo.codingplayboy.com/demo/smallcase/htc/css3_transition_ball.html)，本篇继续深入介绍CSS3 animation，动画相关知识。

## 前言

相对于CSS3 transition，其主要作用于元素状态间的转换，实现的动画主要是在两个状态间的转换，只适合实现一些简单动画，而且通常需要JavaScript配合，对于复杂的动画并不适合；而CSS3 animation则更强大，它允许我们为动画定义许多关键帧，每个关键帧代表动画过程中某个状态，它可以使动画在不同状态间更平滑过渡。

## 创建关键帧（keyframe）

CSS3中，使用@keyframes规则来定义动画及创建关键帧，关键帧以时间百分比排序，即在动画执行时间的百分比时间点触发该动画关键帧，至少定义0%和100%两个关键帧，动画才可用，如下：

```

	@keyframes bounce {
		0% {
			top: 10px;
			left: 10px;
		}
		50% {
			top: 50px;
			left: 50px;
		}
		100% {
			top: 150px;
			left: 150px;
		}
	}
```

如上定义了一个bounce动画，在其内创建了三个关键帧，分别是0%，50%，100%三个时间点，该效果是一个元素渐隐渐现。

## 动画属性

当使用@keyframes规则定义动画关键帧时，必须把该动画绑定到某个选择器，才能有效果，如：

```

	.car {
		position: fixed;
		top: 10px;
		left: 10px;
		z-index: 999;
		width: 60px;
		height: 60px;
		background-color: red;
		opacity: 1;
		border: 1px solid #ccc;
		border-radius: 30px;
		animation: bounce 3s linear 2s infinite alternate;
	}
```

html结构如下：

```

	<div class="car"></div>
```

效果实例查看：[CSS3 animation动画实例](http://demo.codingplayboy.com/demo/smallcase/htc/css3_animation.html)

CSS3中所有动画属性如下：

属性|	描述|  值|
-----|---------|
@keyframes|	规定动画，创建关键帧。| 动画名|	
animation|	所有动画属性的简写属性，除了 animation-play-state 属性。	|动画子属性的合并|
animation-name|	规定 @keyframes 动画的名称。|@keyframes定义的动画名|
animation-duration|	规定动画完成一个周期所花费的秒或毫秒。|默认值为 0。|
animation-timing-function|	规定动画的速度曲线。| 默认值为 "ease"。|
animation-delay|	规定动画何时开始。|默认值为 0。|	
animation-iteration-count|	规定动画被播放的次数。|默认值为 1。	|
animation-direction|	规定动画是否在下一周期逆向地播放。|默认值为 "normal"，"alternate"表示下一周期逆向播放。|	
animation-play-state|	规定动画是否正在运行或暂停。|默认是 "running"。|
animation-fill-mode|	规定元素在动画之后所处的状态。|“forwards”表示元素停留在动画结束的状态，“backwards”表示恢复到在动画开始的状态，默认值为“backwards”。|

如下是上面例子使用动画子属性书写：

```

	.car {
		animation-name: bounce;
		animation-duration: 3s;
		animation-timing-function: linear;
		animation-delay: 2s;
		animation-iteration-count: infinite;
		animation-direction: alternate;
		animation-play-state: running;
	}
```

## 兼容性

Internet Explorer 10、Firefox 以及 Opera 支持 @keyframes 规则和 animation 属性；Chrome 和 Safari 需要前缀 -webkit-；Internet Explorer 9，以及更早的版本，不支持 @keyframe 规则或 animation 属性。

如：

```

	@-webkit-keyframes bounce {
		0% {}
		100%{}
	}
	@keyframes bounce {
		0% {}
		100%{}
	}
	
	.car {
		-webkit-animation: bounce 2s;
		animation: bounce 2s;
	}
```

## 动画事件

和CSS3 transition相似，CSS3还为更好的控制CSS3动画提供了动画事件：animationstart，animationend，animationiteration，我们可以添加事件监听器。

## 小球弹动实例

先看看CSS3 transition过渡实现的小球弹动效果[小球弹动动画](http://demo.codingplayboy.com/demo/smallcase/htc/css3_transition_ball.html)，接下来使用CSS3 animation实现。

html结构如下：

```

	<div class="box"></div>
```

### 创建关键帧

首先创建动画及动画关键帧：

```

	@keyframes bounce {
		0% {
			top: 30px;
		}
		50% {
			top: 300px;
		}
		100% {
			top: 30px;
		}
	}
```

### 动画属性

创建动画后必须绑定到特定选择器，动画才有效果：

```

	.box {
		position: fixed;
		top: 30px;
		left: 30px;
		z-index: 999;
		width: 40px;
		height: 40px;
		background-color: red;
		border: 1px solid #ccc;
		border-radius: 20px;
		-webkit-animation: bounce 2s linear infinite 1s alternate;
		animation: bounce 2s linear infinite 1s alternate;
	}
```

效果可以实现小球弹动，但是缺少弹跳效果，因为动画时间没有为linear，即线性小球下降时没有超重的表现，落地后反弹也没有失重的效果，我们需要为下降和反弹设置不同的动画时间函数，但是在animation属性中只能定义一个动画函数，我们可以在动画关键帧中为每个关键帧定义不同动画时间函数：

```

	@-webkit-keyframes bounce {
		0% {
			top: 30px;
			-webkit-animation-timing-function: cubic-bezier(1, 0, 0.96, 0.91);
		}
		50% {
			top: 300px;
			-webkit-animation-timing-function: cubic-bezier(0, 0.27, 0.32, 1);
		}
		100% {
			top: 30px;
			-webkit-animation-timing-function: cubic-bezier(0, 0.27, 0.32, 1);
		}
	}
	@keyframes bounce {
		0% {
			top: 30px;
			animation-timing-function: cubic-bezier(1, 0, 0.96, 0.91);
		}
		50% {
			top: 300px;
			animation-timing-function: cubic-bezier(0, 0.27, 0.32, 1);
		}
		100% {
			top: 30px;
			animation-timing-function: cubic-bezier(0, 0.27, 0.32, 1);
		}
	}
```

效果如下：[CSS3 animation小球无限弹动](http://demo.codingplayboy.com/demo/smallcase/htc/css3_animation_ball.html)，与之前实例完全相似，而且只通过CSS实现，并没有使用JavaScript。

