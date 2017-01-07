# 优雅的使用Js或CSS处理文本的截断与展示

之前遇见一个问题，就是处理文本截断，然后可以手动切换文本是全展示和手动展示，因为这个问题比较常见，而且其实现方式有多种，于是决定总结一下，与读者分享。

## 前言

首先，我们看这样一个场景：

![展开更多](http://blog.codingplayboy.com/wp-content/uploads/2016/12/expand-more.png)

其html 结构如下：

```

    <p class="text-slice">
        郑成月的经历就是一部中国社会历史！很有价值的！不管是社会研究还是真心纠错，都是非常好的案例。历史就是用各种个人经历写成的，那样就很有实在感。作家们没有素材？都在书写歌功颂德之文？想要把自己的作品流传后世，这就是最好的素材
    </p>
    <label class="expand-more">更多</label>
```

样式如下：

```

    .text-slice {
        display: -webkit-box;
        -webkit-box-orient: vertical;
        -webkit-line-clamp: 3;
        overflow: hidden;
        text-overflow: ellipsis;
    }
```

如图，我们对文字默认展示内容有限制：限制默认最多显示三行，未超出三行时不需要展示更多按钮；超出内容被截断时，可以点击更多，展示全部内容，此时更多文字变成取消，再次点击的时候文字收起。

实现上述功能，常见的是使用JavaScript，本文将介绍使用JavaScript和CSS共三种方式实现：

- Js实现
- CSS :target伪类 实现
- CSS :checked伪类 + ~（临近兄弟节点选择符）实现

## Js实现

先从最常见的Js实现，通常我们绑定点击事件实现切换文本截断与展示全部：

[点击查看效果](http://demo.codingplayboy.com/demo/smallcase/expand_more/expand-js.html)

```

    <script>
        $(function() {
            var $para = $('.content');
            var $more = $('.expand-more');
            $more.on('click', function() {
                $para.toggleClass('text-slice');
                if ($para.hasClass('text-slice')) {
                    $more.html('更多');
                } else {
                    $more.html('收起');
                }
            });
        });
    </script>
```

如上，给按钮绑定点击事件，在事件回调中为文本切换实现截断样式的类名，并同时修改按钮文字，很简单。

但是，到目前为止我们应该发现一个问题吧，假如文本内容少于三行或者刚好三行，我们怎么判断是否需要展示切换按钮呢？

思考。。。。。。

要判断文本是否发生截断，怎么处理呢？
    计算字数肯定不可行，因为涉及到不同手机，不同浏览器，不同字符，会有差异；
    计算高度，比较展示全部和添加截断时的高度值是否相等，相等时，说明不会截断；不相等则说明发生截断。

## 判断文本是否截断

我们不能直接使用该段落元素做判断，用户体验很不好，我们可以添加一个辅助段落元素：

```

    <p class="fake-content text-slice">
        郑成月的经历就是一部中国社会历史！很有价值的！不管是社会研究还是真心纠错，都是非常好的案例。历史就是用各种个人经历写成的，那样就很有实在感。作家们没有素材？都在书写歌功颂德之文？想要把自己的作品流传后世，这就是最好的素材
    </p>
```

样式如下：

```

    .fake-content {
        position: absolute;
        z-index: -1; /* 隐藏 */
        opacity: 0; /* 透明 */
        pointer-events: none; /*元素不可交互/点击*/
    }
```

然后比较此元素在设置截断样式和不设置截断样式时高度，相等即不会发生截断，不相等即发生截断：

```

	// 文本是否发生截断
	function hasTextSliced($ele) {
		var initHeight = $ele.height();
		var height;
		$ele.removeClass('text-slice'); // 删除截断样式
		height = $ele.height();
		
		if (initHeight < height) {
		    // 发生截断
		    return true;
		}
		
		return false;
	}
```

如上，在删除截断样式后，若高度变大，则说明发生文本截断，否则不发生截断。

## CSS实现

CSS可以有两种方式实现：

- :target伪类 实现
- :checked伪类 + ~（临近兄弟节点选择符）实现

[点击查看实例](http://demo.codingplayboy.com/demo/smallcase/expand_more/expand-js.html)

### :target伪类

我们知道HTML中，a链接可以设置锚点，点击后该锚点可以使用```:target```伪类选择获取，于是可以为其设置文字不截断样式：

```

	#content:target {
		overflow: auto;
		display: block;
	}
```

其html结构如下：

```

	<p class="content text-slice" id="content">
   		...        
	</p>
	<p class="fake-content text-slice">
	    ...
	</p>
	<a class="expand-more" href="#content">更多</a>
```

## :checked伪类 + ~选择符

此种方式原理是，选择框可以使用CSS伪类```：checked```获取，然后通过```~```选择符給元素设置样式不截断文本：

```

	#expand-check:checked ~ .content {
	    overflow: auto;
	    display: block;
	}
```

html结构如：

```

	<input type="checkbox" id="expand-check" style="display: none;pointer-events: none;">
	<p class="content text-slice">
	    ...
	</p>
	<p class="fake-content-2 text-slice">
	    ...
	</p>
	<label class="expand-more" for="expand-check">更多</label>
```

如上，通过在文本前面添加一个隐藏的·```<input type="checkbox">元素```，在更多按钮中使用label的for属性，通过设置```<label class="expand-more" for="expand-check">```使其点击时可以改变前面添加的选择框元素的checked状态，从而实现切换效果。

## 总结

不同的方式都能实现效果，那什么情况适合使用哪一种呢，现进行比较：

- 对比Js和CSS方式，Js实现可配置性，操作性强，而CSS实现有一定限制，无法在切换展示的同时进行其他Js处理；但是CSS实现更稳定，高效，代码，出错性小，js错误不影响此功能，在内容展示型页面适合使用。
- 对比:target伪类和:checked伪类，前者代码量少，不需要添加额外元素，但是我们知道锚点点击时，页面是会滚动锚点元素到当前页面顶部的，这不适合长页面。
- :checked伪类方式需要为每一个需要切换展示的段落都添加额外辅助元素，因此不适于在长列表中使用，如评论列表，新闻简介列表页。

[本篇中实例详细代码参考https://github.com/codingplayboy/web_demo/tree/master/smallcase/expand_more](https://github.com/codingplayboy/web_demo/tree/master/smallcase/expand_more)

此篇关于文本截断切换展示的总结，主要出于学习与实用的目的，若喜欢请点赞，文笔水平有限，望包含。