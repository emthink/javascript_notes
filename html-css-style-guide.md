# HTML/CSS 编程风格

HTML/CSS代码风格指导。


## 协议

- 内含资源路径省略协议
	
	```

		<!-- bad -->
		<script src="https://www.google.com/js/gweb/analytics/autotrack.js"></script>

		<!-- good -->
		<script src="//www.google.com/js/gweb/analytics/autotrack.js">
	```

	```

		/* bad */
		.example {
  			background: url(https://www.google.com/images/example);
		}

		/* good */
		.example {
  			background: url(//www.google.com/images/example);
		}
	```

## 通用格式规范

- 代码风格必须统一

- html,css分离

	```
		
		<!-- bad -->
		<h1 style="font-size: 1em;">Html</h1>

		<!-- good -->
		<h1 class="font-1e">Html</h1>
	```

- 一次缩进2个（或4个）空格

	```

		<ul>
		  <li>Fantastic</li>
		  <li>Style Guide</li>
		</ul>

		.content {
		  color: #ff0000;
		}
	```

- html/css标签，属性，样式，选择器字符均使用小写

	```

		<!-- bad -->
		<A HREF="/">Home</A>

		<!-- good -->
		<a href="/test">Home</a>
	```

	```

		/* bad */
		color: #E5E5E5;

		/* good */
		color: #e5e5e5;
	```

- 清除多余空格

## 通用元信息规范

- 文件和文档使用UTF-8格式

- 使用TODO声明所需实现

	```

		<!-- TODO: remove tags -->
		<ul>
		  <li>Fantastic</li>
		  <li>Style Guide</li>
		</ul>
	```

## HTML代码风格

- 文档类型

	```
		
		<!DOCTYPE html>
	```

- 使用语义化标签

	```

		<!-- bad -->
		<p>The Document Header</p>

		<!-- good -->
		<h1>The Document Header</h1>
	```

- 标签嵌套遵循规范

	```
	
		<!-- bad -->
		<span>
			<div></div>
		</span>
		
		<!-- good -->
		<div>
			<div></div>
		</div>
	```

- 为多媒体资源提供备选内容

	```

		<-- bad -->
		<img src="test.png">
	
		<!-- good -->
		<img src="test.png" alt="test">
	```

- 省略引用css样式表和javascript脚本标签的type属性

	```

		<!-- bad -->
		<script src="//www.google.com/js/gweb/analytics/autotrack.js" type="text/javascript"></script>

		<!-- good -->
		<script src="//www.google.com/js/gweb/analytics/autotrack.js"></script>
	```

	```

		<!-- bad -->
		<link rel="stylesheet" href="//www.google.com/css/maia.css" type="text/css">
		
		<!-- good -->
		<link rel="stylesheet" href="//www.google.com/css/maia.css">
	```

- 标签属性值使用双引号

	```

		<!-- bad -->
		<a href='/' class='test'>goto web</a>

		<!-- good -->
		<a href="/" class="test">goto web</a>
	```

- 注释应使用<\!-- -->,且注释内容前后加空格

	```

		<!-- bad -->
		<div class="login">
		...
		</div>

		<!-- good -->
		<!-- 登录区域 -->
		<div class="login"></div>
	```

## CSS代码风格

### ID与Class

- ID,Class名称尽量有意义或者通用；尽量避免无意义和表象的类名或ID值

	```

		/* bad */
		#div-t123 {}
		.button-red {}
		.clear {}

		/* good */
		#login {}
		.btn-error {}
	```

- 使用ID和Class值在满足需求的条件下尽量简短

	```

		/* bad */
		#navigation {}
		.adr {}

		/* good */
		#nav {}
		.address {}
	```

- 在使用较长ID或Class值时，使用连字符‘-’连接

	```

		/* bad */
		.slidewrap {}
		.slideWrap {}
		.slide_wrap {}
		
		/* good */
		.slide-wrap {}
	```

- 样式表中尽量避免使用ID为标签声明样式

- 尽量避免ID或Class与类型选择器混用

	```

		/* bad */
		div.login {}
	
		/* good */
		.login {}
	```	

### 样式规则块

- 每段样式之间应该以空行分隔

	```

		/* bad */
		html {}
		body {}

		/* good */
		html {}

		body {}
	```

- 所有成块样式均应该缩进

	```

		@media screen {
			html {}
		}
	```

### 选择器

- 每一个样式块的选择器后应该加一个空格

	```
		
		/* bad */
		.error{}

		/* good */
		.error {}
	```

- 多个选择器之间以逗号分隔，且每个选择器单独占一行

	```

		/* bad */
		.error, .warn {}

		/* good */
		.error,
		.warn {}
	```

### 样式属性

- 每一个样式属性声明末尾以分号结束

- 属性尽量使用简写方式

	```

		/* bad */
		.login {
			border-style: solid
			border-width: 1px;
			margin-top: 5px;
			margin-left: 10px;
			margin-right: 10px;
			margin-bottom: 5px;
		}

		/* good */
		.login {
			border: 1px solid;
			margin: 5px 10px;
		}
	```

- 属性值应该优先使用单引号，url()内不加引号

	```

		/* bad */
		.error {
			background: url("//www.sohu.com/bg.png");
			font-family: "open sans" helvetica,verdana,san-serif;
		}

		/* good */
		.error {
			background: url(//www.sohu.com/bg.png);
			font-family: 'open sans' helvetica,verdana,san-serif
		}
	```

- 属性值为0时，其后的单位省略

	```

		/* bad */
		.error {
			margin: 0px;
		}

		/* good */
		.error {
			margin: 0;
		}
	```

- 属性值在-1和1之间时，省略前面的0

	```

		/* bad */
		margin: 0.8em;

		/* good */
		margin: .8em;
	```

- 样式规则按类型依次书写

	- Formatting Model 相关属性包括：position / top / right / bottom / left / float / display / overflow 等
     	- Box Model 相关属性包括：border / margin / padding / width / height 等
     	- Typographic 相关属性包括：font / line-height / text-align / word-wrap 等
     	- Visual 相关属性包括：background / color / transition / list-style 等
     	
	```
	
		/* bad */
		.error {
			margin: 0 10px;
			height: 150px;
			color: #fff;
			font-size: 14px;
			left: 10px;
			border: 1px solid #ffccff;
			position: relative;
			top: 5px;
			width: 100px;
			display: block;
		}
		
		/* good */
		.error {
			display: block;
			position: relative;
			top: 5px;
			left: 10px;
			width: 100px;
			height: 150px;
			margin: 0 10px;
			border: 1px solid #ffccff;
			font-size: 14px;
			color: #fff;
		}
	```

### 注释

- 在每一个样式模块前加注释

	```

		/* Header */
		.header {}

		/* footer */
		.footer {}
	```


### 其他

- 样式声明避免使用"!important"

	```

		/* bad */
		.error {
			color: #333 !important;
		}

		/* good */
		.error {
			color: #333;
		}
	```
