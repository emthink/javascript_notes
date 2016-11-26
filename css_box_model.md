# CSS盒模型（BOX Model）

CSS盒模型描述的是为文档树中的元素创建并根据[CSS视觉格式化模型（visual formatting model）](http://blog.codingplayboy.com/2016/11/06/css_visual_formatting_model/)布局的矩形盒子。

## 盒子尺寸（dimensions）

每个盒子有一个内容区域（content area），如，文本，图片等，还包括可选的四周内间距，边框，外间距区域。每个区域的尺寸结构如下图（盗个图先）：

![CSS盒模型](http://blog.codingplayboy.com/wp-content/uploads/2016/11/boxdim.png)

如上图，外间距，边框，内间距都被拆分成上下左右四部分。

## 边界

内容，内间距，边框，外间距四个区域都有四个边界（edge）,分别是上右下左边界。

### 内容边界（content edge or inner edge)

内容边界围绕由盒子宽高占据的矩形，这通常由元素的渲染内容（rendered content）决定,四个内容边界定义了这个盒子的内容盒（content box）。

#### 渲染内容（Rendered content）

> The content of an element after the rendering that applies to it according to the relevant style sheets has been applied.
How a replaced element's content is rendered is not defined by this specification.
Rendered content may also be alternate text for an element (e.g., the value of the XHTML "alt" attribute), and may include items inserted implicitly or explicitly by the style sheet, such as bullets, numbering, etc.

> 所谓渲染内容，就是对某个元素的内容根据相关样式表进行渲染。对于置换元素内容其渲染内容则有些不同，如```<img>```元素，若其图片加载失败，则渲染内容会被替换成其alt属性值，当然渲染内容也可能包含其他通过样式表插入的内容，如:after等伪元素插入的额外内容。
 

### 内间距边界（padding edge）

内间距边界围绕盒子的内间距，如果padding值为0，则内间距边界和内容边界重合，四个内间距边界定义了这个盒子的内间距盒（padding box）。

### 边框边界（border edge）

边框边界围绕盒子的边框，如果border宽度值为0，则边框边界与内间距边界重合，四个边框边界定义了这个盒子的边框盒（border box）。

### 外间距边界（margin edge or outer edge）

外间距边界围绕盒子的外间距，如果margin宽度为0，则外间距边界与边框边界重合，四个外间距边界定义了这个盒子的外间距盒（margin box）。

 
## 区域尺寸

一个盒子的内容区域尺寸，即内容宽度和高度，由以下因素决定：

- 1.创建这个盒子的元素是否设置width和height属性
- 2.这个盒子是否包含文本或其他盒子
- 3.这个盒子是否是表格

内容，内间距和边框区域的背景样式由创建盒子的元素的background属性值决定，而外间距的背景总是透明的。

### margin

margin属性定义一个盒子的外间距区域的宽度，分为margin-top, margin-right, margin-bottom, margin-left四部分，其值可以是一个固定长度也可以是百分比值或auto，允许负值。

- 长度

	一个固定长度值，如10px

- 百分比

	可以定义一个百分比值，这个百分比是相对于这个盒子的包含块的宽度。

- auto

	自动计算

 该属性值对非置换行内元素无效。

- 用法：

	属性值如果是一个值，适用于上右下左四部分；如果是两个值，则第一个值是上下的值，第二个是左右的值；如果是三个值，则第一个为上值，第二个为左右值，第三个为下值；如果是四个值，则依次为上右下左值。

#### 折叠（collapse）margin

CSS中，两个或多个相邻的外间距，之间会发生崩塌，形成一个单一的最大外间距，这就是折叠（collapse）。详见[CSS格式化上下文](http://blog.codingplayboy.com/2016/11/05/css_formattingcontext/)。

### padding

padding属性定义一个盒子的内间距区域的宽度，分为padding-top,padding-right，padding-bottom，padding-left四部分。其值可以是一个固定长度也可以是百分比值，不允许负值。

- 长度

	一个固定长度值，如10px

- 百分比

	可以定义一个百分比值，这个百分比是相对于这个盒子的包含块的宽度。

### border

border属性定义一个盒子的边框区域的宽度（border-width），颜色(border-color)，风格样式(border-style)，border属性是这三个属性的简写，分为border-top,border-right,border-bottom,border-left四部分。

#### border-width

定义边框宽度，其值可以为thin或medium或thick或一个长度值，不允许负值。该属性包括border-top-width, border-right-width, border-bottom-width, border-left-width四部分，如：

```

	div { border-width: thin }                   /* thin thin thin thin */
	div { border-width: thin thick }             /* thin thick thin thick */
	div { border-width: thin thick medium }      /* thin thick medium thick */
```

#### border-color

定义边框颜色，其值可以为一个color值或transparent

- color

	可以为十六进制颜色值或rgb,rgba,hsl,hsla等值。

- transparent

	该值表明边框为透明的。

- border-style
	
	- none

		无边框，边框宽度的计算值为0。

	- hidden

		同 'none'。

	- dotted

		点边框

	- dashed

		虚线边框

	- solid

		单实线边框

	- double

		双实线边框

	- inset

		内嵌

	- outset

		和'inset'相反: 外散边框

## 行内元素的盒模型

在每一个行盒中，用户代理（如浏览器）必须为每个元素创建行内盒并且按照视觉顺序渲染盒子的外间距，边框和内间距。

### direction

当元素的direction属性为ltr时，第一个行盒创建的最左边的盒子拥有左外间距，左边框，左内间距，最后一个行盒的最右的的盒子拥有右外间距，右边框，右内间距；

- 当元素direction属性值为rtl时，第一个行盒创建的最右边的盒子拥有右外间距，右边框，右内间距，
- 最后一个行盒的最左的的盒子拥有左外间距，左边框，左内间距。

 

 

 

 

 

 