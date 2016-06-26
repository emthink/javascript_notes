# HTML 之文档类型（DOCTYPES）
在编写HTML文档时，在html标签之前，我们都知道要加入<!DOCTYPE>声明，即DTD（Document Type Definition）声明。<!DOCTYPE> 声明不是 HTML 标签；它是指示 web 浏览器页面使用哪个 HTML或XHTML 版本进行编写的指令，浏览器才会使用合适的文档类型和方式来处理、解析网页。

> 在文档中添加文档类型声明，使网页标准化、合法化。

## 文档类型声明（DTD声明）

HTML4.01有三种文档类型：strict、transitional和frameset。XHTML1.0也有这三种类型，但XHTML1.1只有一种文档类型。

*注：请务必将文档类型定义放在文件头部，在DOCTYPE之前的任何东西，包括注释或者其他声明，都将导致IE9或更早的浏览器触发怪异模式。*

- HTML4.01 Strict DTD：

	该 DTD 包含所有 HTML 元素和属性，但不包括展示性的和弃用的元素（比如 font）。不允许框架集（Framesets）。
	
	```
	
		<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
	```

- HTML4.01 Transitional DTD：

	该 DTD 包含所有 HTML 元素和属性，包括展示性的和弃用的元素（比如 font）。不允许框架集（Framesets）。
	
	```
	
		<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	```

- HTML4.01 Frameset DTD：

	该 DTD 等同于 HTML 4.01 Transitional，但允许框架集内容。
	
	```
	
		<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Frameset//EN" "http://www.w3.org/TR/html4/frameset.dtd">
	```

- XHTML1.0 Strict DTD：

	该 DTD 包含所有 HTML 元素和属性，但不包括展示性的和弃用的元素（比如 font）。不允许框架集（Framesets）。必须以格式正确的 XML 来编写标记。
	
	```
	
		<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
	```

- XHTML1.0 Transitionl DTD：

	该 DTD 包含所有 HTML 元素和属性，包括展示性的和弃用的元素（比如 font）。不允许框架集（Framesets）。必须以格式正确的 XML 来编写标记。
	
	```
		
		<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
	```
	
- XHTML1.0 Frameset DTD：

	该 DTD 等同于 XHTML 1.0 Transitional，但允许框架集内容。
	
	```
	
		<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Frameset//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd">
	```
	
- XHTML1.1 DTD：

	该 DTD 等同于 XHTML 1.0 Strict。
	
	```
	
		<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">
	```
    
- HTML5 DTD

    到了HTML5，DTD声明已经统一了，其仅仅用来触发浏览器标准模式：
    
    ```
    
        <!DOCTYPE html>
    ```
    
## 标准模式与怪异模式

目前浏览器布局引擎有三种模式：怪异模式（quirks mode）、准标准模式（Almost standards mode）、标准模式（standards mode）。

> 在怪异模式，为了，兼容标准出现之前的网站，浏览器布局会模拟IE5的非标准模式；在标准模式，浏览器布局遵循HTML和CSS规范；在准标准模式，会出现部分怪异模式行为。

对于一个HTML文件，其会根据文件头部的DOCTYPE来选择使用怪异模式或者标准模式来处理页面。

了解更多，请查阅[怪异模式](https://developer.mozilla.org/en-US/docs/Mozilla/Mozilla_quirks_mode_behavior)与[标准模式](https://developer.mozilla.org/en-US/docs/Gecko's_Almost_Standards_Mode)之间的差别
    
## 讨论

每一个页面都要有文档类型声明，这个声明必须放在所有HTML元素之前，浏览器才能正确解析页面。XHTML要求必须有DTD声明，否则浏览器会以quirk模式（怪异模式）解析页面，没有定义文档类型，或文档类型定义版本有误，都有可能导致浏览器以quirk模式错误的解析、渲染页面。