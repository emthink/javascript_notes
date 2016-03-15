# HTML5 window.postMessage与跨域问题
在前一篇文章中，讲到浏览器同源策略，即阻止不同域的页面间访问彼此的方法和属性，并对解决同源策略跨域问题提出的解决方案进行阐述：子域代理、JSONP、CORS。本篇将详细阐述HTML5 window.postMessage，借助postMessage API，文档间可以以安全可控的方式实现跨域通信，第三方JavaScript代码也可以与iframe内加载的外部文档进行通信。

[先来看一个postMessage实例](http://jhssdemo.duapp.com/demo/h5_postmessage/index.html)

[有需要可以看看相关代码](https://github.com/codingplayboy/javascriptDemo/tree/master/html5/html5_message)

## HTML5 window.postMessage API

> HTML5 window.postMessage是一个安全的、基于事件的消息API。

### postMessage发送消息
在需要发送消息的源窗口调用postMessage方法即可发送消息。

- 源窗口   
源窗口可以是全局的window对象，也可以是以下类型的窗口:
    - 文档窗口中的iframe:
    
    ```

        var iframe = document.getElementById('my-iframe');
        var win = iframe.documentWindow;
    ```
    
    - JavaScript打开的弹窗：
    
    ```
    
        var win = window.open();
    ```
    
    - 当前文档窗口的父窗口：
    
    ```
    
        var win = window.parent;
    ```
    
    - 打开当前文档的窗口：
    
    ```
        
        var win = window.opener();
    ```

找到源window对象后，即可调用postMessage API向目标窗口发送消息：

    ```win.postMessage('Hello', 'ttp://jhssdemo.duapp.com/');```
    
postMessage函数接收两个参数：第一个为将要发送的消息，第二个为源窗口的源。

*注：只有当目标窗口的源与postMessage函数中传入的源参数值匹配时，才能接收到消息。*

### 接收postMessage消息
要想接收到之前源窗口通过postMessage发出的消息，只需要在目标窗口注册message事件并绑定事件监听函数，就可以在函数参数中获取消息。

```

    window.onload = function() {
		var text = document.getElementById('txt');	
		function receiveMsg(e) {
			console.log("Got a message!");
			console.log("\nMessage: " + e.data);
			console.log("\nOrigin: " + e.origin);
			// console.log("Source: " + e.source);
			text.innerHTML = "Got a message!<br>" +
				"Message: " + e.data +
				"<br>Origin: " + e.origin;
		}
		if (window.addEventListener) {
            //为窗口注册message事件，并绑定监听函数
			window.addEventListener('message', receiveMsg, false);
		}else {
			window.attachEvent('message', receiveMsg);
		}
	};
```

message事件监听函数接收一个参数，Event对象实例，该对象有三个属性：

- data   发送的具体消息
- origin  发送消息源
- source  发送消息窗口的window对象引用

### 说明

- 可以将postMessage函数第二个参数设为*，在发送跨域消息时会跳过对发送消息的源的检查。
- postMessage只能发送字符串信息。


### 实例

- 源窗口

```

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
    	<title>Html5 postMessage</title>
    	<style>
    		#otherWin {
    			width: 600px;
    			height: 400px;
    			background-color: #cccccc;
    		}
    	</style>
    </head>
    <body>
    	<button id="btn">open</button>
    	<button id="send">send</button>
    	 <!-- 通过 iframe 嵌入子页面(接收消息目标窗口) --> 
    	 <iframe src="http://jhssdemo.duapp.com/demo/h5_postmessage/win.html" 
    				 id="otherWin"></iframe> 
    	 <br/><br/> 
    	 <input type="text" id="message"><input type="button" 
    			 value="Send to child.com" id="sendMessage" /> 
    	<script>
    		window.onload = function() {
    			var btn = document.getElementById('btn');
    			var btn_send = document.getElementById('send');
    			var sendBtn = document.getElementById('sendMessage');
    			var win;
    			btn.onclick = function() {
                    //通过window.open打开接收消息目标窗口
    				win = window.open('http://jhssdemo.duapp.com/demo/h5_postmessage/win.html', 'popUp');
    			}
    			btn_send.onclick = function() {	
    				// 通过 postMessage 向子窗口发送数据		
    				win.postMessage('Hello', 'http://jhssdemo.duapp.com/');
    			}
    			function sendIt(e){ 
    		 		// 通过 postMessage 向子窗口发送数据
    		 		document.getElementById("otherWin").contentWindow 
    			 	.postMessage( 
    				 	document.getElementById("message").value, 
    					"http://jhssdemo.duapp.com/"); 
    			} 
    			sendBtn.onclick = function(e) {
    				sendIt(e);
    			};
    		};
    	</script>
    </body>
    </html>
```

- 目标窗口

win.html
```

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
    	<title>Html5 postMessage</title>
    	<style>
    		#txt {
    			width: 500px;
    			height: 300px;
    			background-color: #cccccc;
    		}
    	</style>
    </head>
    <body>
    	<h1>The New Window</h1>
    	<div id="txt"></div>
    	<script>		
    		window.onload = function() {
    			var text = document.getElementById('txt');  
                //监听函数，接收一个参数--Event事件对象
    			function receiveMsg(e) {
    				console.log("Got a message!");
    				console.log("\nMessage: " + e.data);
    				console.log("\nOrigin: " + e.origin);
    				text.innerHTML = "Got a message!<br>" +
    					"Message: " + e.data +
    					"<br>Origin: " + e.origin;
    			}
    			if (window.addEventListener) {
                    //为window注册message事件并绑定监听函数
    				window.addEventListener('message', receiveMsg, false);
    			}else {
    				window.attachEvent('message', receiveMsg);
    			}
    		};
    	</script>
    </body>
    </html>
```

### 回顾
通过本篇的学习，了解了使用HTML5的postMessage API在窗口间进行通信，也知道可以借助其实现跨域通信；现代浏览器基本都支持postMessage，而对于一些老式浏览器如IE7-等，可以使用一定的替代方案，进行数据通信，如window.name、url查询字符和hash片段等。