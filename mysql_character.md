# MySQL编码设置
MySQL是使用最多的数据库之一，我们开发使用MySQL数据库时，经常会出现中文乱码的问题，本篇就本人开发实际经验介绍解决方法。MySQL的默认编码是latin1，不支持中文，需要我们手动将其设置为utf-8。

![MySQL默认编码](http://blog-resource.bj.bcebos.com/photos/2016/02/mysql-char1.png)

## Windows环境

- 关闭mysql服务

通过cd命令进入mysql安装目录下，执行net stop mysql命令：

