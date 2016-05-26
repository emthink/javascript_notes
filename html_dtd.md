# HTML 之文档类型（DOCTYPES）
在编写HTML文档时，在html标签之前，我们都知道要加入<!DOCTYPE>声明，即DTD（Document Type Definition）声明。<!DOCTYPE> 声明不是 HTML 标签；它是指示 web 浏览器页面使用哪个 HTML 版本进行编写的指令，浏览器才会使用合适的文档类型和方式来处理、解析网页。

> 在文档中添加文档类型声明，使网页标准化、合法化。

## 文档类型声明（DTD声明）

HTML4.01有三种文档类型：strict、transitional和frameset。XHTML1.0也有这三种类型，但XHTML1.1只有一种文档类型。


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