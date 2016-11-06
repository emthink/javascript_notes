# Framekiller

Framekiller是一种web应用使用来防止应用页面在frame窗口内展示的技术。

它通常被用来阻止第三方网站的frame窗口在未经允许的情况下就被加载，所以这也是防止[点击劫持（clickjacking）](https://en.wikipedia.org/wiki/Clickjacking)的一种手段。

## clickjacking

点击劫持是一种恶意手段，欺骗用户从当前安全页面点击第三方恶意插入的不安全的内容，他们就可以获取用户信息或控制他们的计算机。

这是一个典型的的浏览器安全问题，在2008年提出，通常是恶意的第三方在用户不知道的情况下通过在当前页面插入恶意的脚本或代码，比如在页面插入一个按钮，或使用一个div覆盖在当前页面原始内容之上，用户点击的时候执行他们插入的恶意函数和代码。

[更多相关知识可以查看。](https://en.wikipedia.org/wiki/Clickjacking)

## 代码实现

framekiller主要使用JavaScript实现，方法有多种：

### 方法一

该方法通过验证当前窗口是否是应用主窗口，来决定是否显示当前页面，进而实现效果：

```

    <style>html {display: none;}</style>
    <script>
        if (self == top) {
            document.documentElement.style.display = 'block';
        }else {
            top.location = self.location;
        }
    </script>
```

### 方法二

另一个方法是允许用户检测是否启用framekiller:

```

    var framekiller = false;
    window.onbeforeunload = function() {
        if(framekiller) {
            return '';  // any message that helps user to make decision
        }
    };
```

在添加iframe后，应该加入如下代码：

```

    // 实际frame元素的ID
    var frmaeId = 'my-frame';
    document.getElementById(frameId).onload = function() {
      framekiller = true;
    };
```

## Anti-Framekiller与sandbox

html5给iframe新增了一个sandbox属性,通过设置该属性值，可以为iframe内容设置不同的权限。

### iframe

> The HTML Inline Frame Element (iframe) represents a nested browsing context.

> iframe表示一个嵌套浏览上下文，即在当前页面插入另一个HTML页面。

每个浏览上下文都有各自的会话历史信息和document，包含嵌入内容的浏览上下文称为父级浏览上下文，顶级浏览上下文，通常就是浏览器窗口环境。

在HTML5以后，应尽可能少用iframe，以下情况除外：

- 1.跨域通信
- 2.引用第三方内容
- 3.独立的子窗口应用
- 4.在移动端用于从网页调起客户端应用
- 5.沙箱隔离:

### 沙箱隔离

所谓的沙箱隔离，就是将当前页与iframe内容页分隔开来，形成两个相对独立的部分，即通过添加iframe的sandbox属性。

#### sandbox

其值可以为空字符串或以空格分隔的多个字符串。

若指定为空字符串，则使用所有限制条件，其属性值有很多，主要的有以下几种：

- allow-forms:允许iframe内表单提交
- allow-scripts:允许iframe内脚本执行
- allow-same-origin:允许iframe内容被当作与包含文档同源，否则是跨域
- allow-top-navigation:允许嵌入浏览上下文导航（加载）内容到包含文档或完成页面跳转
- allow-modals:允许嵌入上下文打开模态窗口
- allow-popups:允许嵌入上下文弹出窗口
- allow-orientation-lock:允许嵌入上下文取消锁定屏幕方向
- allow-pointer-lock：允许锁定鼠标

如：

主页面代码：

```

    <body>
        <iframe src="./iframe.html" width="" height="" sandbox="allow-top-navigation allow-scripts allow-same-origin"></iframe>
    </body>
```
 
iframe页面内容：

```

    <body>
        <h1>Iframe Sandbox</h1>
        <form action="/ta" method="post" >
            <input type="text" />
            <input type="submit" value="Submit" />
            <a href="http://blog.codingplayboy.com" target="_top">GoTo</a>
        </form>
        <script type="text/javascript">
            (function() {
                var p = window.parent;
                var w = p.document.location;
                console.log(w);
            })();
        </script>
    </body>
```


**链接：[iframe sandbox](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe#attr-sandbox)  [framekiller](https://en.wikipedia.org/wiki/Framekiller)**