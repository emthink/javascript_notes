# CSS3 transform

之前，我们介绍过[CSS3的transition过渡效果](http://blog.codingplayboy.com/2016/07/03/css3%E4%B9%8Btransition/)，本篇深入介绍CSS3 transform，即变换。

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

## 小球弹动

还记得之前使用CSS3 transition实现过[小球弹动动画]


