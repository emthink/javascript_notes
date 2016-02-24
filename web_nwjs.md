# 初识Node-Webkit之桌面应用程序开发

近期要开发一个桌面客户端类型的项目，调研发现了NW.js，即node-webkit，融合了 Node.js 和 Webkit HTML 渲染器来运行本地应用，是一个Chromium和node.js上的结合体。本篇通过一个案例，简单熟悉一下NW.js。

## NW.js

在[NW.js官网](http://nwjs.io/)([http://nwjs.io/](http://nwjs.io/))下载node-webkit包， 选择与平台相对应的版本，下载并安装即可。以windows版本为例，解压后目录结构如下:

![node-webkit](http://blog-resource.bj.bcebos.com/photos/2016/02/nwjs_pak.png)

## Hello World

### 初始化项目
在任一目录下，新建index.html和package.json文件：

![NW.js项目](http://blog-resource.bj.bcebos.com/photos/2016/02/nwjs_pro.png)

package.json为NW项目的配置入口：

```

    {
        "title": "Hello World",
    	"name": "app",
    	"main": "index.html", //项目入口窗口所在文件
    	"window": {
    		"toolbar": false, //工具条
    		"frame": false, //边框，与transparent联合使用，控制窗口边框显示
    		"transparent": true 
    	}   
    }
```
[更多信息可参见文档](https://github.com/nwjs/nw.js/wiki/Manifest-Format)

### 打包
新建zip压缩包，包名自定义，建议与package.json文件中name属性保持一致，本文以app.zip为例，然后将以上index.html和package.json文件放入，如下：

![NW.js项目](http://blog-resource.bj.bcebos.com/photos/2016/02/nwjs_prozip.png)

*注：项目文件必须在压缩包根目录下，即通过鼠标右键新建ZIP压缩文件生成压缩包，然后将文件移入压缩包。*

随后将该app.zip包修改后缀为.nw,即：

![app.nw](http://blog-resource.bj.bcebos.com/photos/2016/02/nwjs_pronw.png)

将该nw包拖放到解压后的nw环境包中的nw.exe，即启动运行,：

![Hello World案例运行](http://blog-resource.bj.bcebos.com/photos/2016/02/nwjs_pro_run.png)

### 生成exe文件

如上已经生成一个可运行的项目文件，但是否感觉运行比较麻烦？接下来我们可以进一步生成.exe文件，就像普通exe文件一样点击即可运行。

![NW项目生成exe文件](http://blog-resource.bj.bcebos.com/photos/2016/02/nwjs_exepak.png)

进入对应目录，执行copy /b app.nw+nw.exe app.exe命令，即生成可执行app.exe文件，双击即可运行，运行结果与上文一样。


## 优缺点思考

- 优点
    * 提高UI开发效率与视觉效果，使用HTML5和CSS3可以极大提高UI开发效率并提升传统桌面客户端视觉效果
    * 容易实现跨平台：Windows、Linux、Mac OS X
    * 方便开发人员调试

- 缺点
    * 程序体积变大，就如上文的一个简单地示例程序，都大于20M
    * 使用Js使用API操作客户端，安全性需要保证


对node-webkit的初步认识就到这里，其他使用web技术开发桌面程序的还有Electron、hex等，若感兴趣可以学习一下，希望能有所得。





