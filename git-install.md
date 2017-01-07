# Git由浅入深之安装与配置

上一篇介绍了版本控制系统相关知识，本篇主题是如何安装和配置Git。

## 使用

使用方式主要分为两种：

- 原始命令行方式（使用命令行工具）
- 图形界面方式（使用客户端）

目前有很多客户端工具可以使用如，但是大多数都只支持一些基本的操作，

为了更全面的学习和深入理解Git，我们主要介绍命令行方式，这才是合理打开Git方式。

## 安装

[各安装包下载地址](https://git-scm.com/downloads)

#### Linux

```

	sudo yum install git-all
	or for Ubuntu
	sudo apt-get install git-all
```

[了解Linux更多类型安装方式](https://git-scm.com/download/linux)

#### Mac

Mac下可以有以下两种方式安装：

- 安装Xcode命令行工具
- [下载安装包](https://git-scm.com/download/mac)

#### Windows

Windows下安装方式主要有以下两种：

- [下载安装包安装](http://git-scm.com/download/win)
- [安装Github,Github安装包，既包含命令行，也包含GUI(即其客户端)](http://windows.github.com).

另外，还可以通过源码方式安装，但是比较麻烦，且对于学习和使用Git并无多大帮助，所以不做介绍，有兴趣可以搜索了解。

## 配置

当你看到这里时，我相信你的笔记本已经安装好了Git,直接进入本节主题：Git的自定义配置。

> Git支持我们设置配置变量来对Git的外观和操作等诸多方面进行配置，而且配置会持久保存，你也可以随时更改配置。

#### git config

Git提供```git config```工具支持我们获取或设置Git配置变量，这些配置变量保存在三个不同地方：

- /etc/gitconfig文件

      -  该文件包含系统上的每一个用户及其所有项目仓库的配置变量；
      -  使用```git config --system```时，读写的是此文件中的配置变量。

- ~/.gitconfig 或 ~/.config/git/config文件

       - 特定用户的全局配置变量文件；
       - 使用```git config --global```时，读写此文件中配置变量；
       - 在Windows系统上，该文件在Git安装时指定的安装目录下，默认是在系统环境变量$HOME目录下，如C:\Users\$USER

- .git/config文件

       - 特定项目仓库的配置文件
       - 存放在当前用户的Git目录中，Git目录在上一节中讲到。

以上三层配置优先级由低到高，即当前项目的配置优先于其他配置。

#### 查看配置

我们可以查看Git当前配置列表，输入如下指令：

```

	git config --list
```

如图：

![git config --list](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-config-list.png)

当然，也可以查看知道配置项，如查看用户名称：

```

	git config user.name
```

![git config user.name](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-config-uname.png)

#### 配置项

所有的配置都有两种方式设置，可自由选择：

- 直接修改上文提到的配置文件；
- 通过命令行进行设置

以下所有操作均通过命令行设置。

##### 用户信息配置

在使用Git前我们应该配置一下用户名和邮箱：

```

	git config --global user.name "惊鸿"
	git config --global user.email "jinghong@example.com"
```

这里使用了```--global```参数，是针对此计算机上用户的全局配置，如果我们希望在不同项目使用不同身份信息，则直接在该项目下配置：

```

	git config user.name "jh"
	git config user.email jh@example.com
```

##### 配置别名

有时候我们也许会觉得重复输入一些长指令，比较费劲，Git支持我们为其设置别名，需要使用```alias```指令，如：

```

	git config --global alias.co checkout
```

如上配置，是将Git的检出指令（checkout）命名别名为co，配置后，如下两个指令是等效的：

```

	git co xxx
	git checkout xxx
```

当然，我们不只是能对单个指令指定别名，还可以为复合指令指定别名，如：

```

	git config alias.acm '!'"git add . && git commit"
```

配置后```git acm```等效于如下两条指令：

```

	git add .
	git commit
```

##### 编辑器

我们也可以指定我们在Git编辑文本时使用的默认编辑器，如果不指定，则会使用系统的默认文本编辑器。

比如配置为notepad++，只需如下配置：

```

	git config --global core.editor "'C:/Program Files (x86)/Notepad++/notepad++.exe' -multiInst -nosession"
```

**注意：需包围应用程序路径的单引号，以及windows默认的路径分隔符 \有转义的含义，所以要改成 / 。**

当然，我推荐你使用vim编辑，这样不需要考虑跨平台使用：

```

	git config --global core.editor vim
```

如上文所述，你还可以通过文件直接设置，如打开.git/config文件，在core中添加 editor=vim，查看.git/config文件：
  
![cat .git/config](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-config-editor.png)

##### 帮助

可以使用Git帮助工具，查看某指令信息：

```

	git help <verb>
	or
	git <verb> --help
	or
	man git-<verb>
```

本篇介绍了不同环境下Git的安装及安装后的Git自定义配置，下一篇将会介绍Git的基础工作理论，敬请关注。
