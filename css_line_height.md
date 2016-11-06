# line-height

对于line-height属性，作为一个前端开发人员，肯定都不陌生，天天都在使用的一个CSS属性，但是这也是一个很考验一个前端开发者CSS基础功力的知识点，本篇就对line-height进行详细学习。

## 认识line-height

line-height，顾名思义，是定义某元素行高的，即，行与行之间的间距。我们知道HTML中元素分为块级与行内元素，行内元素又分为置换行内元素和非置换行内元素，对于不同类型元素，line-height作用并不相同：

- 块级元素： 对于块级元素，line-height属性定义了该元素内部所有line boxes（独占一行的盒子，即行盒模型）的最小高度。
- 非置换行内元素（non-replaced inline element）： 对于非置换行内元素，如，line-height属性定义line box的高度。
- 置换行内元素(replaced inline element)： 对于置换行内元素，如img，button,a等，line-height属性不影响其行高。

## 行内元素

块级元素与行内元素的区别，相信大家都知道，块级元素，单独成行展示，行内元素则是在同一行内并列展示。但行内元素又分为置换行内元素和非置换行内元素，大家可能对这两个概念不太清楚，这里进行展开阐述：

### 置换与非置换行内元素

> There are two types of inline elements: replaced inline elements and non-replaced inline elements. In general, non-replaced elements are those whose content is contained in the document, whereas replaced-elements are those whose content is outside of the document，such as an image, embedded document, or applet. 
However, as far as the inline box model is concerned, the definitions are as described above except that elements with display types  inline-table and inline-block (the latter is a proposed type for CSS3 to accommodate form elements) are considered replaced elements.

> 行内元素可以分为两种类型：置换行内元素和非置换行内元素。通常，非置换元素的内容直接包含在当前文档内，而置换元素的内容则来自文档外，比如`<img src="" />`，潜入文档或者小应用。但是，例外的是，涉及到行内盒模型时，display属性为inline-table和inline-block（CSS3中推荐form元素使用此类型）的元素都被当作置换元素。

## line-height属性值

line-height属性值是可继承的，其值有多种，包括normal数值，百分比，特定长度：

### normal

normal是line-height属性初始值，其用于计算样式时的具体值通常由用户代理决定，许多桌面浏览器通常初始该值为1.2，但这个值这也取决于文元素的font-family值。

### 数值

line-height属性值可以是一个不带单位的number数字，计算的值是该属性值乘以元素的字体大小（font-size）。这种类型的值也是通常建议使用的方式。

### 百分比

line-height属性值也可以是百分比形式，与当前元素的字体大小有关，计算的值是元素的计算字体乘以该百分比值。

### 长度

line-height属性值还可以设定特定长度值，该指定的值用于计算line box的高度。

## 计算







