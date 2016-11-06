# CSS之视觉格式化模型(Visual Formatting Model)

CSS中的visual formatting model(视觉格式化模型)是一种处理文档并把它显示在可视化媒体上的算法。这是CSS中的一个基础概念。visual formatting model把文档的每一个元素转换成0个，1个或多个符合CSS盒模型的盒子，每个盒子的布局内容包括如下部分：

- 盒子尺寸：明确指定受限制或不指定
- 盒子类型：行内，行内级，原子行内级，块盒
- 定位方案：包括正常文档流，float或者绝对定位
- 节点树的其他元素：其子元素或兄弟元素
- viewport（视口）的尺寸和定位
- 内含图片的固有尺寸
- 其他额外信息


## 视口（viewport）

---

用户代理，如浏览器通常提供给用户一个viewport（屏幕上的一个窗口或其他可视区域），通过该viewport用户可以访问到一个文档，在viewport尺寸变化时，用户代理可以改变文档的布局。

### 滚动（scroll）

当viewport宽度或高度小于渲染文档的画布时，默认地，用户代理会提供一个滚动机制。

## 包含块（containing block）

在CSS2.1中，许多盒子的定位和尺寸是根据一个矩形盒子的边界来计算的，这个矩形盒子就叫做包含块（containing block）。通常，一个盒子为其子级盒子创建包含块，我们就可以说这个盒子为其后代创建一个包含块。

### 初始包含块（initial containing block）

文档根元素所在的矩形包含块就是初始包含块，对于浏览器等媒体，其尺寸等于viewport尺寸并且被固定在画布，其direction属性值和根元素一样。

### 溢出（overflow）

visual formatting model相对于包含块的边界来渲染盒子，然而一个盒子并不受其包含块限制，当一个盒子的内容超出其包含块时，就是我们通常所说的溢出（overflow）。



## 盒子的生成

----
CSS visual formatting model从文档元素生成盒子，生成的盒子有多种类型，其类型会影响visual formatting model的行为。生成盒子的类型取决于元素CSS属性display的值。

### 块级元素和块级盒

当元素的CSS属性display计算值是block，list-item或table的时候，这个元素就是块级元素。一个块级元素会被视觉格式化成一个块（block），按照垂直方向排列。

#### 主要块级盒（principal block-level box）

每个块级盒子都参与[Block Formatting Context(块格式化上下文)](http://blog.codingplayboy.com/2016/11/05/css_formattingcontext/)。每个块级元素(block-level element)至少生成一个块级盒子，即主要块级盒，大多数元素只生成一个主要块级盒，而有一些元素，如li列表，则会生成多个盒子，包括主要块级盒及描述排版信息或项目符号的其他盒子。

主要块级盒包括包括后代元素生成的盒子及生成的内容，我们可以使用之前谈到的定位方案定位该盒子。

#### 块容器盒（block container box）

块容器盒只包含其他块级盒或生成一个行内格式化上下文，即只包含行内盒。我们必须明白块级盒和块容器盒两个概念不冲突。块级盒描述盒子与它的父级和兄弟元素之间的行为，块容器盒描述它与其后代元素之间如何相互作用。块级盒也可能是一个块容器盒。一些块级盒，比如表格，不是块级容器盒；相反地，一些块级容器盒，比如非置换行内块和非置换表格单元格也不是块级盒。

#### 块级盒与块盒

块级盒中也是块容器盒的盒子，是块盒（block box）。

#### 匿名块盒

在某些情况下，visual formatting model 需要添加一些辅助性盒子，但是CSS选择器不能选中这些盒子，这些盒子叫做匿名盒子（anonymous box）.

不能使用CSS选择器选择匿名盒子，说明不能给他们定义样式，这意味着所有可继承属性都继承自父元素，所有不可继承属性都是其初始值。

##### 匿名块盒实例

- 1.一种情况是块级盒子与行内级盒子同时存在是，在行内级元素周围会形成匿名块盒。

块容器盒只能包含块级盒或行内级盒，但是，通常在一个文档中，包含这两种类型盒子，此时，在行内级盒子前后创建匿名块盒。

```

    <div>我是第一个匿名块盒的内容<p>我是块级盒子的内容</p>我是第二个匿名块盒的内容</div>
```

效果如下：

![匿名块盒](http://blog.codingplayboy.com/wp-content/uploads/2016/11/anonymous_block_box.png)

上例中，在div块盒中，产生了一个p块级盒，两个匿名块级盒,一个是包裹p元素前的行内文本，盒模型结构如下：

![匿名块盒](http://blog.codingplayboy.com/wp-content/uploads/2016/11/anonymous_block_box_note.png)

对于p块级盒，我们可以通过CSS属性改变它的字体大小/颜色，背景色等样式；然而我们并不能控制两个匿名块级盒的样式，其可继承样式属性继承自其父元素div,比如字体的颜色（color）,其他不可继承属性则被设置成初始值，比如背景色（background-color），通常该值的初始值是transparent(透明)，这也是为什么在初始值时，我们能看见div的背景色。

- 2.另一种产生匿名块盒的情况是一个行内盒包含一个或多个块盒

当一个行内盒只包含一个块盒时，包含块盒的盒子会被分成两个行内盒子，分别在被包含块盒的前后。
被包含块盒前后的行内盒子都会包裹在一个匿名块盒中，所以被包含块盒就与包含行内元素的匿名块盒成了兄弟级盒子。

若包含多个块盒，且块盒之间没有行内内容，就只会在所有块盒的前面和后面分别产生一个匿名块盒。

如下实例p元素display为inline，span元素display属性值为block:
```

    <p style="display: inline;">我是第一个匿名块盒的内容 <span style="display: block;">我是块级盒子的内容</span>我是第二个匿名块盒的内容</p>
```

效果如下：

![匿名块盒](http://blog.codingplayboy.com/wp-content/uploads/2016/11/anony_block_box_2.png)

这个实例会产生两个匿名块盒，一个是包裹span元素前面的行内文本，一个包裹span元素后面的文本，盒结构如下：

![匿名块盒](http://blog.codingplayboy.com/wp-content/uploads/2016/11/anonymous_block_box_2_note.png)

### 行内级元素和行内级盒

当一个元素的CSS display属性计算值是inline或inline-block或inline-table时，这个元素就是行内级元素。行内级元素不包含块内容，它和其他行内级内容分布在一行内，直至超出换行，典型的比如段落，通常由图片，文字等行内级元素组成。

行内级元素产生行内级盒，行内级盒参与[行内格式化上下文（inline formatting context）](http://blog.codingplayboy.com/2016/11/05/css_formattingcontext/)。

#### 行内级盒与行内盒

行内级盒中，有些盒子其内容参与其容器的行内格式化上下文，这些行内级盒子，就是行内盒子。

**所有的display值为inline的非置换盒子都是行内盒。**

##### 原子行内级盒（atomic inline-level box）

行内级盒中，那些内容不参与形成行内格式化上下文的盒子，称为原子行内级盒子；由置换行内级元素或display计算值为inline-block或inline-table的元素产生；原子行内级盒不会被分割成多个盒子。在一行宽度足够显示其内容时，原子行内级盒内容不会被拆分成多行。

如下，span的display属性值为inline，即其默认值时，其形成的盒子不是原子行内级盒，因此会被拆分成多行：

```

    <div style="width:18em;">
    对于非原子行内级盒 <span style="display: inline;color: red;">我的内容会被拆分成多行    </span> 因为这里的span是inline行内盒.
    </div>
```

效果如图：

![非原子行内级盒](http://blog.codingplayboy.com/wp-content/uploads/2016/11/not_atomic_inline_level_box.png)

而对于如下，将span元素display设置成inline-block;

```

    <div style="width:18em;">对于原子行内级盒 <span style="display: inline-block;color: red;">我的内容不可以被拆分成多行</span> 因为这里的span是inline-block行内级盒.
    </div>
```

效果如图：

![非原子行内级盒](http://blog.codingplayboy.com/wp-content/uploads/2016/11/atomic_inline_level_box.png)

#### 匿名行内盒（anonymous inline box）

类似于上文说到的块盒，有时候CSS引擎也会自动创建行内盒,这些行内盒子不能使用CSS选择器选中，不能被命名，它们的可继承CSS属性值继承自父级元素，不可继承属性则是初始值，这就是匿名行内盒。

创建匿名行内盒，最常见的情况就是，某些文本作为一个块盒的直接子级，创建一个行内格式化上下文,这种情况下，文本就包含在匿名行内盒中。特别需要注意的是，即使是空格组成的文本也会生成匿名行内盒，除非我们使用white-space属性将空白内容清除。

### 其他类型的盒子

#### 行盒

行盒是行内格式化上下文创建来表示一行文本。在一个块盒中，一个行盒从块盒的某一边框扩展到另一边框。当使用了float定位时，行盒从左浮动的最右边框扩展到右浮动的最左边框。行盒只是一种技术上的实现，通常并不需要我们进行操作。

#### 插入盒（Run-in box）

插入盒，使用display:run-in;定义，可能是块盒也可能是行内盒，取决于其后续盒子的类型。最典型的应用场景是在文章的第一段中定义文章的标题。由于可操作性不足，Run-in box在CSS2.1中被移除，但也可能在后续版本重新出现，可以了解一下。

## 内容模型（content model）

---

内容模型，由元素类型及其内容布局触发，除了前文提到的行内和块级格式化上下文，CSS还定义了其他的内容模型，这些模型用来描述特定的内容布局，也可能会创建其他类型盒子：

- 表格内容模型：可能创建一个表格容器盒和一个表格盒，也可能创建标题caption盒
- 多列内容模型：可能在容器盒子和内容之间创建多个列盒
- 实验性的grid或flex盒子内容模型，也可能创建其他类型的盒子

一旦创建了盒子，CSS引擎就会使用以下某一种算法进行布局定位：

- 1.普通流定位，默认按从上到下，从左到右的顺序一个接一个的放置盒子
- 2.float浮动定位，使盒子脱离普通流，将盒子放置在包含盒的两边。
- 3.绝对定位，使用包含盒创建的绝对坐标系统定位盒子，绝对定位的盒子是会层叠覆盖的。

[更多关于CSS定位机制，可以查看CSS之定位机制](http://blog.codingplayboy.com/2016/01/19/css_position/)

### 普通流

在普通文档流中，盒子是一个接一个排版。在块级格式化上下文中，它们按垂直方向排列，在行内格式化上下文中，按水平方向排列。当元素的position属性值是stati或relative时，触发普通流定位。

### float浮动

在float浮动定位中，浮动盒子会被放置在当前行的首尾两端。float定位会导致普通流中的内容沿着浮动盒子的边界排列，除非使用clear属性等方法清除浮动影响。

### 绝对定位

在绝对定位中，盒子完全脱离普通文档流，不再发生影响。绝对定位使用相对于其包含盒的top,bottom,lefl,right属性定位。position属性值为absolute或fixed的元素是绝对定位元素，fixed定位元素包含块是viewport，其相对于viewport视口进行定位。


关于CSS的视觉格式化模型就介绍到这，下一篇，计划总结一下CSS盒子模型相关知识，希望能給读者有所帮助。
