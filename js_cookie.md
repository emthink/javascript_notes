# JavaScript之Cookie详解
本篇我们要介绍的是Cookie，其常被用来保存用户登录状态，存储购物车状态等等。
JavaScript中Cookie，是一种浏览器提供的机制，其把document的cookie属性暴露给JavaScript，JavaScript可以对其进行操作。

## Cookie存储
Cookie存储在本地终端，浏览器会将Cookie的key/value保存到某个目录下的文本文件内，一般以以域名进行区分，即一个域名一个Cookie文件。windows 7下cookie存储目录为：C:\Users\Administrator\AppData\Roaming\Microsoft\Windows\Cookies，部分目录被隐藏，可直接在文件资源地址栏输入访问，Administrator为系统用户名。

![windows7 cookie目录](http://blog-resource.bj.bcebos.com/photos/2016/02/js_cookie.png)

## Cookie格式
Cookie在本地文件是以键值对形式存储的，那通过JavaScript获取的完整Cookie是以什么形式表现的呢？如下是一个完整的Cookie：

`"name=testCookie; date=test20160201; city=Beijing"`

完整的Cookie，以字符串形式表示，以分号加空格分隔每个键值对，键与值之间用等号连接。

在浏览器控制台可查看Cookies:
Name为Cookie键，Value为对应的值；Domain为Cookie对应的所在域；Path为Cookie在本地存储路径：/为默认路径；Expires/Max-Age为Cookie有效时间；Size为Cookie大小。
![控制台Cookies](http://blog-resource.bj.bcebos.com/photos/2016/02/js_cookies.png)

## Cookie操作
在JavaScript中，可对Cookie进行增删查改相关操作，如下：
### 获取完整Cookie
完整Cookie，可通过document下属性cookie获取：

```

    var cookieStr = document.cookie;
    console.log(cookieStr);
```
### 修改Cookie
以下addCookie，当不存在接收参数键时，即新创建Cookie键值对；存在时，即更新cookie键值对：
```

    /**
     * [addCookie 编辑Cookie]
     * @param {[type]} name        [Cookie键]
     * @param {[type]} value       [Cookie值]
     * @param {[type]} expireHours [Cookie键值有效期]
     */
    function addCookie(name, value, expireHours) {
        var cookieString = name + "=" + encodeURI(value);
        //判断是否设置过期时间
        if (expireHours && expireHours > 0) {
            var date = new Date();
            date.setTime(date.getTime() + expireHours * 3600 * 1000);
            cookieString = cookieString + "; expires=" + date.toUTCString();
        }
        document.cookie = cookieString;
    }
    addCookie('name', 'test', 1);
```
*注：cookie中值可能会有特殊字符，如等号，所以需经过encodeURI编码处理。*

### 获取特定键的Cookie值
getCookie在传入参数为空或Cookie中不存在传入参数键时获取完整Cookie，存在则获取特定键对应的Cookie值：

```

    /**
     * [getCookie 获取Cookie]
     * @param  {[type]} name [Cookie键]
     * @return {[type]}      [Cookie值]
     */
    function getCookie(name) {
        var strCookie = document.cookie;
        var arrCookie = strCookie.split("; ");
        for (var i = 0; i < arrCookie.length; i++) {
            var arr = arrCookie[i].split("=");
            if (arr[0] == name) {
                return decodeURI(arr[1]);
            }
        }
        return strCookie;
    }
    getCookie(); //输出完整Cookie
```
*注：可能存在特殊字符，在创建Cookie时进行了encodeURI编码，此处返回值需decodeURI解码处理。*

### 删除特定Cookie键值对
此deleteCookie参数为必需：
```

    /**
     * [deleteCookie 删除Cookie]
     * @param  {[type]} name [Cookie键]
     * @return {[type]}      []
     */
    function deleteCookie(name) {
        var date = new Date();
        date.setTime(date.getTime() - 5000);
        document.cookie = name + "=delete; expires=" + date.toUTCString();
    }
    deleteCookie('name');
```

需要注意的是，浏览器对于Cookie大小有限制，一般不大于4K，且其安全性不高，在HTML5中提出的localStorage是一个很不错的替代品。
