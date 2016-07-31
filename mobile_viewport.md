# 移动开发之viewport


我们知道PC浏览器上网站想要在手机浏览器上友好访问，以前是通过调整内容实现适配；后来iphone提出在“虚拟窗口”（视口）上展现网页内容，并提供viewport元信息元素以控制虚拟窗口大小。

## 话说viewport


浏览器默认把viewport设置为一个较宽的值980px或1024px，大于设备屏幕上的可见宽度，在没有添加viewport元信息标签的移动端网页，显示会有横向滚动条。

在移动端网站上，我们常看见：

```

	<meta name="viewport", width=device-width>
```

其中width是指定当前浏览器布局视口的宽度，device-width是指设备的CSS像素宽度。

在讲解viewport之前，需要了解一些相关概念：

- 物理像素（physical pixel）

	物理像素又称设备像素，指设备每英寸拥有的像素数量，其值越高，表明屏幕密度越高。

- CSS像素

	CSS像素又称设备独立像素（device independent pixel），简称DIPs，是一个相对单位，是浏览器上度量页面内容的单位之一。是计算机程序使用的一种虚拟像素。

- 密度独立像素（density independent pixel）

	密度独立像素也称设备无关像素，是计算机屏幕的一点，由程序使用虚拟像素（CSS像素）表示，然后经由计算机系统转换成物理像素。

- 屏幕密度

	屏幕密度指一个设备表面上存在的像素数量。也称分辨率。

- 屏幕像素密度（dot per inch）

	屏幕像素密度，简称dpi，单位是ppi（pibel per inch），指屏幕对角线方向上每英寸的设备像素数。

	> dpi（dot per inch）是指每英寸的像素
	
	如iphone6，分辨率是640\*1136，屏幕尺寸为4英寸，则其ppi为：

	![iphone6 dpi](http://blog-resource.bj.bcebos.com/photos/2016/07/dpi.png)

- 设备像素比（device pixel ratio）

	设备像素比简称dpr，指物理像素与设备独立像素（CSS像素）的比；
	dpr = 物理像素/CSS像素
	
	iphone3分辨率为320\*480，1 物理像素 = 1 CSS像素，dpr=1；iphone4屏幕大小不变，分辨率为640\*960，1 CSS像素 = 2 物理像素，即dpr=2。

	一般通过window.devicePixelRadio可以获取dpr，但也需注意兼容性问题。

### 视口

在桌面浏览器中，浏览器窗口就是我们CSS布局时，使用相对宽度能使用的最大宽度。在移动浏览器中则不同，我们可以使用viewport控制视口。

### 视觉视口（visual viewport）

关于visual viewport的定义如下：

> The visual viewport is the part of the page that’s currently shown on screen. The user may scroll to change the part of the page he sees, or zoom to change the size of the visual viewport。

> 视觉视口是当前屏幕上页面的可见部分。用户可以滚动来改变当前页面可见部分，或者缩放来改变视觉视口的尺寸。

visual viewport宽度可以通过window.innerWidth来获取，其值是屏幕中CSS像素的数量。用户可以通过缩放来改变visual viewport的尺寸，缩小时，visual viewport值变大，放大时，visual viewport值变小。

### 布局视口（layout viewport）

layout viewport的定义如下：

> In the CSS layout, especially percentual widths, are calculated relative to the layout viewport, which is considerably wider than the visual viewport.

> 在CSS布局中，比如百分比宽度是相对于布局视口来计算的，布局视口通常比视觉视口宽。

layout viewport宽度可以通过document.documenElement.clientWidth来获取。

### 理想视口（ideal viewport）

> 理想视口，是指设备的屏幕尺寸，单位为物理像素。

ideal viewport宽度可以使用screen.width来获取，其值是由设备决定，是对设备来说最理想的布局视口尺寸。


```

	<meta name="viewport", width=device-width>
```
device-width返回的就是设备的ideal viewport宽度。