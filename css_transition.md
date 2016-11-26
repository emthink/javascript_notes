# CSS3之transition

JavaScript是单线程执行的，代码是一句一句执行的，而异步任务，如定时器或者动画，会被压入执行队列，在线程空闲时执行。在CSS3动画出现之前，我们通常使用定时器和JavaScript实现动画效果，但是由于JavaScript单线程执行，很容易发生动画卡顿，效果较差。CSS3提供的过渡（transition）、动画（animation）、变换（transform）能比较好的为我们展现不同的动画效果，因此有必要对其进行了解、学习，本篇开始介绍CSS3之过渡。

## 过渡（transition）

> CSS过渡支持我们在改变CSS属性时控制动画的变化效果。这种效果可以在鼠标单击、获得焦点、被点击或对元素任何改变中触发，并圆滑地以动画效果改变CSS的属性值。

CSS过渡在独立于JavaScript执行线程的另一个线程里执行，JavaScript的执行不会阻塞CSS动画，使得应用动画效果更加流畅。

#### CSS3过渡简洁语法如下：

```

    transition: [property] [duration] [timing-function] [delay];
```

- property，属性名称；
- duration，过渡动画持续的时间；
- timing-function，动画执行时间变化函数；

    - ease-in，动画变化逐渐变快；
    - ease-out，动画变化逐渐变慢；
    - ease-in-out，动画先逐渐变快然后逐渐变慢；
    - linear，线性动画变换；
    - cubic-bezire(x1, y1, x2, y2)，定义动画时间变化曲线
    - step(number, type)，定义动画变化区段块

    关于动画变化函数更多，请查看[https://developer.mozilla.org/en-US/docs/Web/CSS/timing-function](https://developer.mozilla.org/en-US/docs/Web/CSS/timing-function)	

- delay，过渡动画属性变化延迟执行时间。

*注：可以一次声明多个过渡变化，以逗号分隔。*

#### CSS3过渡子属性语法

我们可以通过CSS过渡子属性分别控制CSS3过渡动画模块：

- transition-property：定义将要使用CSS3过渡动画的单个或多个CSS属性名称
- transition-duration：定义发生过渡动画的单个或多个CSS属性所对应过渡动画执行所需时间
- transition-timing-function：定义CSS3过渡动画过程中属性值与时间的变化函数
- transition-delay： 定义过渡动画属性变化延迟执行时间


## CSS3过渡动画实例

[观看CSS3过渡实例请进入](http://demo.codingplayboy.com/demo/smallcase/htc/css_transition.html)

html代码：

```

	<body>
	<p>The box below combines transitions for: width, height, background-color, transform. Hover or click over the box to see these properties animated.</p>
    <button class="trans_btn" onclick="document.querySelector('.box').className='trans box'">单击触发过渡动画</button>
    <br><br>
    <div class="box"></div>
    <br>
</body>
```

CSS样式：

```

	.box {
		    border: 1px solid #FFCCCC;
		    display: block;
		    width: 100px;
		    height: 100px;
		    background-color: #0000FF;
		    -webkit-transition: width 2s, height 2s, background-color 2s, -webkit-transform 2s;
		    transition: width 2s, height 2s, background-color 2s, transform 2s;
		}

		.box:hover {
		    background-color: #FFCCCC;
		    width: 200px;
		    height: 200px;
		    -webkit-transform: rotate(180deg);
		    transform: rotate(180deg);
		}
		.trans.box {
			background-color: #FF0000;
		    width: 300px;
		    height: 300px;
		    -webkit-transform: rotate(270deg);
		    transform: rotate(270deg);
		}
```

## CSS3过渡与transitionend

CSS属性发生过渡变化时，除了会执行CSS相应的指定变化，还会在完成时触发transitionend事件。

##### transitionend

> transitionend 事件在 CSS 完成过渡后触发。如果过渡在完成前移除，例如transition-property属性被移除，此事件将不被触发。

语法如下：

```
	
	// Safari 3.1 到 6.0 
	object.addEventListener("webkitTransitionEnd", myScript); 
	// 标准方式
	object.addEventListener("transitionend", myScript);       
```

#### 无限弹动小球实例

[无限弹动小球实例地址](http://demo.codingplayboy.com/demo/smallcase/htc/css3_transition_ball.html)

html代码：

```

	<div class="ball"></div>
	<div class="floor"></div>
```

css样式：

```

	* {
			margin: 0;
			padding: 0
		}
		html, body {
			width: 100%:
			height: 100%;
		}
		.ball {
			background-color: blue;
			width: 100px;
			height: 100px;
			position: absolute;
			top: 30px;
			left: 20px;
			border-radius: 50px;
		}
		.floor {
			position: absolute;
			bottom: 10px;
			left: 0;
			width: 100%;
		}
```

JavaScript代码：

```

	<script>
		window.onload = function() {
			var down = false;
			var transition = 'transition';
			var transitionend = 'transitionend';
			var ball = document.querySelector('.ball');
			var floor = document.querySelector('.floor');

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
		    EventHandler(window);

		    //叛定浏览器前缀
			if (typeof document.body.style.webkitTransition === 'string') {
				transition = 'webkitTransition';
				transitionend = 'webkitTransitionEnd';
			}else if (typeof document.body.style.MozTransition === 'string') {
				transition = 'MozTransition';
			}

			//小球弹动实现
			function bounceBall(ball) {
				var ball = ball || document.querySelector('.ball');
				if (down) {
					ball.style[transition] = 'top 1s cubic-bezier(0, 0.27, 0.32, 1)';
					ball.style.top = '30px';
					down = false;
				}else {
					ball.style[transition] = 'top 1s cubic-bezier(1, 0, 0.96, 0.91)';
					ball.style.top = (floor.offsetTop - 100) + 'px';
					down = true;
				}
			}

			//注册transitionend事件，实现无限弹动效果
			EventHandler(ball, transitionend, function(e) {
				bounceBall(e.target);
			});
			EventHandler(ball, 'click', function(e) {
				bounceBall(e.target);
			});

			bounceBall(ball);
		};
	</script>
```

本篇对CSS3过渡进行了基本讲解和拓展，并给出了两个实例，供参考。