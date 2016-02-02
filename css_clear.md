# CSS之清除浮动
对于CSS中的float属性，想必都很熟悉了，float: left | right;设置元素浮动常用来页面开发布局或定位元素，功能是很强大的，但是在使用过程中也经常会碰到问题，如下这种情况：

![float浮动常见问题](http://blog-resource.bj.bcebos.com/photos/2016/02/css_clear1.png)

代码如下：

```

    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="UTF-8">
        <title>CSS之清除浮动</title>
        <style>
    	* {
			margin: 0;
			padding: 0;
		}
		html, body {
			width: 100%;
			height: 100%;
			text-align: center
		}
		.cont {
			background-color: gray;
			width: 80%;
			margin: 20px auto 0 auto;
			text-align: center;
		}
		.left {
			background-color: red;
			width: 80%;
			float: left;
			height: 200px;
		}
		.right {
			float: right;
		}
	</style>
    </head>
    <body>
        <div class="cont">
    		<div class="left">float: left</div>
    		<div class="mid">Mid</div>
    	</div>
    </body>
    </html>
```
如上父容器元素高度未设置，容器中有浮动的子元素，父容器并没有高度自适应以容纳所有子元素，浮动元素溢出从而影响整个布局，这就是浮动溢出问题，而解决问题的方法就是所谓的清除浮动。

## 清除浮动
如何清除浮动，解决方法一大堆，此处就我个人经验及学习总结，大致分为3类：

- 设置clear: left | both | right;清除浮动

主要是给浮动元素后面普通流元素设置clear属性，清除浮动：

```

    .mid {
        clear: both;
    }
```
也有人提出在浮动元素后面增加一个空div，设置其clear: both;清除浮动，其实本质上都是一样。

- 