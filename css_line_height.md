# CSS基础line-height和vertical-align属性

对于line-height属性，作为一个前端开发人员，肯定都不陌生，天天都在使用的一个CSS属性，但是这也是一个很考验一个前端开发者CSS基础功力的知识点，本篇就对line-height进行详细学习。

关于本篇中涉及到的行盒盒模型相关概念可以查看[CSS盒模型（BOX Model）](http://blog.codingplayboy.com/2016/11/26/css_box_model/)。

## 认识line-height

若对基础概念不感兴趣，可以直接查看实例和总结。

line-height，顾名思义，是定义某元素行高的，即，行与行之间的间距。我们知道HTML中元素分为块级与行内元素，行内元素又分为置换行内元素和非置换行内元素，对于不同类型元素，line-height作用并不相同：

1. 块级元素： 对于块级元素，line-height属性定义了该元素内部所有line boxes（独占一行的盒子，即行盒模型）的最小高度。
2. 非置换行内元素（non-replaced inline element）： 对于非置换行内元素，如，span，line-height属性定义行盒（line box）的高度。
3. 置换行内元素(replaced inline element)： 对于置换行内元素，如img，button,a等，line-height属性不影响其行高。

## 行内元素

块级元素与行内元素的区别，相信大家都知道，块级元素，单独成行展示，行内元素则是在同一行内并列展示。但行内元素又分为置换行内元素和非置换行内元素，大家可能对这两个概念不太清楚，这里进行展开阐述：

### 置换与非置换行内元素

> There are two types of inline elements: replaced inline elements and non-replaced inline elements. In general, non-replaced elements are those whose content is contained in the document, whereas replaced-elements are those whose content is outside of the document，such as an image, embedded document, or applet. 
However, as far as the inline box model is concerned, the definitions are as described above except that elements with display types  inline-table and inline-block (the latter is a proposed type for CSS3 to accommodate form elements) are considered replaced elements.

> 行内元素可以分为两种类型：置换行内元素和非置换行内元素。通常，非置换元素的内容直接包含在当前文档内，而置换元素的内容则来自文档外，比如`<img src="" />`，潜入文档或者小应用。但是，例外的是，涉及到行内盒模型时，display属性为inline-table和inline-block（CSS3中推荐form元素使用此类型）的元素都被当作置换元素。

## line-height属性值

line-height属性值是可继承的，除了设置inherit关键字外，其值有多种，包括normal数值，百分比，特定长度：

### normal

normal是line-height属性初始值，其用于计算样式时的具体值通常由用户代理决定，许多桌面浏览器通常初始该值为1.2，但这个值这也取决于元素的font-family值，其效果与设置数字值相同。

### 数字值

line-height属性值可以是一个不带单位的number数字，计算的值是该属性值乘以元素的字体大小（font-size）。这种类型的值也是通常建议使用的方式，后代元素继承该line-height数字值，并与自己font-size相乘得到计算值。

### 百分比

line-height属性值也可以是百分比形式，与当前元素的字体大小有关，计算的值是元素的计算字体乘以该百分比值，后代元素line-height直接继承该计算后的值。

### 长度

line-height属性值还可以设定特定长度值，该指定的值用于计算line box的高度，后代元素会直接继承该计算后的值。

## 实例

首先看个实例，[点击传送到实例](http://demo.codingplayboy.com/demo/smallcase/htc/line-height.html)

点此查看完整代码 [https://github.com/codingplayboy/web_demo/blob/master/css/line-height.html](https://github.com/codingplayboy/web_demo/blob/master/css/line-height.html)

效果如图：

![line-height实例](http://blog.codingplayboy.com/wp-content/uploads/2016/12/line-height.png)

可以看到：

1. 当line-height值使用长度值时，当前元素内的行盒行高是该值，其后代元素均继承该值，行高不会随自身字体尺寸变换而变换；
1. 当line-height值使用百分比时，当前元素内的行盒行高是百分比乘以其font-size值，其后代元素继承的则是这个计算后的值，行高也不会随自身字体尺寸变换而变换；
1. 当line-height值使用数字值时，后代元素继承该数字值，行高是各自font-size值乘以该数值，行高与字体尺寸相适应。

**由此得出，我们推荐line-height属性值使用数字值。**

## vertical-align

vertical-align属性指定行内元素或表格单元格元素在垂直方向的对齐方式，其值主要有baseline
，middle，top，bottom等，

为如下值时，相对于其父元素对齐：

1. baseline：将此元素与其父元素按基线对齐
    1. 基线，一个元素的基线是指正常流中其包含的最后一个行内元素的基线。可以查看[使用text-align实现两端对齐布局实例中使用vertical-allign使其垂直方向对齐](http://blog.codingplayboy.com/2016/10/01/justify_layout/)；
    1. 若元素没有基线（如，没有正常流中的行内元素或其overflow属性不为visible），则将其外间距的下边界当作基线
1. middle：将行内盒中线与父元素基线加上父元素中x字母尺寸的一半高度对齐  ；
1. sub：将此元素基线与父元素的下标基线对齐；
1. super：将此元素基线与父元素的上标基线对齐；
1. text-top：将此元素的顶部和父元素的内容区域顶部对齐；
1. text-bottom：将此元素的顶部和父元素的内容区域底部对齐；
1. 百分比：在基线对齐的基础上，将此元素的基线（也等同于整个行内盒）提高或降低一个高度值，此高度值为百分比乘以其自身line-height计算值（正值为提高，负值时降低），值为0%时，等同于baseline；
- 长度值：在基线对齐的基础上，将此元素的基线（也等同于整个行内盒）提高或降低一个高度值，值为0px时，等同于baseline；
    
为如下值时，相对于整行（所在行盒）对齐：

1. top：将当前元素及其后代元素的顶部与行盒顶部对齐；
1. bottom：将当前元素及其后代元素的顶部与行盒顶部对齐



