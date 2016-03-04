# Web学习之跨域问题及解决方案

在做前端开发时，我们时常使用ajax与服务器通信获取资源，享受ajax便利的同时，也知道它有限制：跨域安全限制，即同源策略。

> 同源策略（SOP）,核心是确保不同源提供的文件之间是相互独立的

> 默认情况下，XHR对象只能访问与包含它的页面处于同一域中的资源，这种限制可以预防某些恶意攻击，但同时也带来很多不便。

本篇对于常见的解决浏览器跨域问题的方案进行总结阐述。

## 常见解决跨域问题的方案
在web开方中，解决跨域问题最常见的方法有：

- document.domain+iframe（子域名代理）
- jsonp实现跨越
- postMessage实现跨越
- CORS服务端解决跨域

接下来对以上常用方式进行详细阐述：

### 1.document.domain + iframe（子域名代理）
对于主域相同而子域不同的情况，可以通过设置document.domain的办法来解决。如：对于两个文件http://www.a.com/a.html和http://blog.a.com/b.html均加上设置document.domain = 'a.com'；然后在a.html文件中创建一个iframe，通过iframe两个js文件即可交互数据：

```

    //www.a.com/a.html
    <script>
        document.doamin = 'a.com';
        var iframe = document.createElement('iframe');
        iframe.src = 'http://blog.a.com/b.html';
        document.body.appendChild(iframe);
        iframe.onload = function() {
            var doc = iframe.contentDocument || iframe.contentWindow.document;
            // 在这里操纵b.html
            console.log(doc);
        };
    </script>
```
在blog.a.com/b.html内编写代码：

```
    
    //blog.a.com/b.html
    <script>
        document.domain = 'a.com';
    </script>
```

备注：某一页面的domain默认等于window.location.hostname。主域名是不带www的域名，例如a.com，主域名前
面带前缀的通常都为二级域名或多级域名，例如blog.a.com其实是二级域名。 domain只能设置为主域名，不可以在
blog.a.com中将domain设置为test.a.com。

问题：
	1、安全性，当一个站点（b.a.com）被攻击后，另一个站点（c.a.com）会引起安全漏洞。
	2、如果一个页面中引入多个iframe，要想能够操作所有iframe，必须都得设置相同domain。

### 2.jsonp实现跨域
JSONP，即带填充的JSON，可以在JavaScript中绕过同源策略，发起跨域HTTP请求。

> JSONP，一个无关标准，使用脚本标签来跨域获取数据的技术。

同源策略有一个显著例外：HTML脚本元素是可以规避SOP检查的。这意味着我们可以通过加载外部JavaScript文件的方式来向其他源发出HTTP请求。

```<script>http://blog-resource.bj.bcebos.com/files/2016/03/info.js</script>```

```

    //http://blog-resource.bj.bcebos.com/files/2016/03/info.js
    var jsonpData = {
        name: 'jsonp',
	    title: 'JSONP data'
    };
```

- 动态的回调函数

JSONP通过script元素加载JSON，通过脚本URL的查询字符串，服务器将响应封装在回调函数中，而回调函数名由请求者在URL查询字符串中给出，此回调函数，即填充。填充可以是任何有效的JavaScript表达式，最常见的是变量和回调函数。

```

    <script>
        var jsonpCallback = function(data) {
            console.log('The response data: ' + JSON.stringify(data));
        };
        var script = document.createElement('script');
        script.async = true;
        script.src = 'http://blog-resource.bj.bcebos.com/files/2016/03/info2.js?callback=jsonpCallback';
        document.body.appendChild(script);
    </script>
```
在全局作用域下定义回调函数，当HTML DOM中加入script标签时发起请求，服务端可以通过URL获取回调函数名，并生成返回如下类似内容：

```

    jsonpCallback({
        name: 'jsonp',
        title: 'JSONP data'
    });
```
如php实现：

```
    
    <?php
        header("Content-type: application/javascript");
        $callback = $_GET['callback'];
        $data = json_encode(array(
            'name' => 'jsonp,
            'title' => 'JSONP data'
        ), JSON_PRETTY_PRINT);
        echo "$callback($data);";
    ?>
```

```

    $.ajax({    
           url: '',  // 跨域URL   
           type: 'GET',    
           dataType: 'jsonp',    
           jsonp: 'jsonpcallback', //默认callback   
           data: mydata, //请求数据   
           timeout: 5000, 
           success: function (json) { //客户端jquery预先定义好的callback函数，成功获取跨域服务器上的
    json数据后，会动态执行这个callback函数    
               if(json.actionErrors.length!=0){    
                   alert(json.actionErrors);    
               }    
           },    
           complete: function(XMLHttpRequest, textStatus){    
                 
           }
       });
```

### 3.服务器端的跨域解决方案

跨域资源共享（CORS）定义了浏览器和服务器如何通过可控方式进行跨域通信。CORS通过添加特殊HTTP头信息以允许浏览器和服务器判断请求是成功还是失败。几乎所有现代浏览器都支持CORS。

- HTTP请求

当跨域发送HTTP请求时，支持CORS的浏览器会通过，添加额外Origin头信息指定请求源，其值包括请求协议、域名、端口。如：

```Origin: http://www.codingplayboy.com```

若请求头中无Origin信息，服务器将不反悔任何CORS头信息。

服务端接收到请求时会检查头信息确定是否接受请求：若接受请  求，必须返回一个包含Access-Control-Allow-Origin响应头，其值与请求头Origin值相同，如：

```Access-Control-Allow-Origin: http://www.codingplayboy.com```

对于公共资源且允许任何源请求数据，服务器通常返回该值为通配符*，如：

````Access-Control-Allow-Origin: *```

浏览器接收到服务器HTTP响应时，首先会检查Access-Control-Allow-Origin的值，只有当值存在且同Origin匹配，才继续处理该请求。

下面是一段使用CORS跨域请求的代码：  
```

    function createCORS(url, method) {
        if (typeof XMLHttpRequest === 'undefined') {
            return null;
        }
        //标准浏览器
        var xhr = new XMLHttpRequest();
        if ('withCredentials' in xhr) {
            xhr.open(method, url, true);
        }else if (typeof XDomainRequest !== 'undefined') {
            //IE
            xhr = new XDomainRequest();
            xhr.open(method, null);
        }else {
            //不支持CORS
            xhr = null;
        }
        return xhr;
    }
    var req = createCORS('http://blog.codingplayboy.com', 'GET');
    if (req) {
        req.onload = function() {
            
        };
        req.send();
    }
```

Cookie及HTTP认证头
我们还需要知道不同于普通的HTTP请求，使用CORS发送请求时，默认情况下，浏览器不会携带任何Cookie和HTTP认证头等识别信息，只有当我们将XMLHttpRequest对象的withCredentials属性值设为true，才在该请求发送时添加额外识别信息。

```
    
    var xhr = new XMLHttpRequest();
    xhr.withCredentials = true;
```

若服务器需要识别信息，则在响应头中添加Access-Control-Allow-Credentials响应头：

```Access-Control-Allow-Credentials: true```

若发送请求时将withCredentials设为true，服务端没有返回该响应头，浏览器将拒绝该响应。

本来一切都是美好的，然而XDomainRequest不支持withCredentials属性，于是IE8、IE9并不支持请求时包含识别信息。

- 预检请求

使用CORS时，若请求方法不是GET，POST或HEAD，又或者使用了自定义HTTP头，浏览器将发起预检请求。

> 预检请求是一个服务端认证机制，在执行请求之前会判断该请求是否合法。

发送复杂请求时，浏览器会将原始请求的方法和请求头作为预检请求的信息发送给服务器；服务器需要决定是否接受该请求并响应，预检请求通常是使用一种OPTIONS的HTTP方法。

客户端发起复杂请求时，会先发起预检，携带以下头信息:

|请求头                 |描述
|-----------------------------------|---------------
|Origin                             |请求源
|Access-Control-Request-Method      |请求方法（HTTP方法）
|Access-Control-Request-Headers     |请求自定义头（以逗号分隔）

服务器返回的响应头：

|响应头                 |描述
|-----------------------------------|---------------
|Access-Control-Allow-Origin        |允许的请求源（与请求头Origin匹配）
|Access-Control-Allow-Methods       |允许的请求方法（以逗号分隔）
|Access-Control-Allow-Headers       |允许的头信息（以逗号分隔）
|Access-Control-Max-Age             |预检请求的缓存时间（单位：秒）
|Access-Control-Allow-Credentials   |指定请求是否支持认证信息（可选）

客户端接收到服务端的响应后，会使用之前声明的HTTP方法和请求头发送真正的请求。

*预检请求会被浏览器缓存，缓存时间为Access-Control-Max-Age值，缓存时间内，后续相同类型的请求不再发起重复的预检请求。IE8、IE9均不支持预检请求。*

### 4.HTML5 postMessage
HTML5的window.postMessage为浏览器带来了一个安全的、基于事件的消息API。与之前的子域名代理通过iframe跨子域通信不同，使用postMessage不再是直接访问一个文档的属性和方法，而是向文档发送消息然后等待响应，这要求形成一条双向的通信通道。 
更多有关postMessage的内容将在下一篇阐述。