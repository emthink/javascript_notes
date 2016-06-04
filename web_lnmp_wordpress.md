# LNMP搭建WordPress博客
几经折腾，我的个人博客从最开始的利用github搭建，然后租用云主机，再到现在购买服务器，安装LNMP环境，搭建WordPress博客。所谓LNMP，就是Linux、Nginx、MySQL和世界上最好的语言--PHP。此次部署博客前后遇到问题不少，花了两周时间，所得不少，对Linux也有更多的了解，但更感前路漫漫，尚需努力。

## Linux

- CentOS中启动、停止、重启服务方法：

```

    systemctl start|stop|restart|enable service //依次为启动、停止、重启、设置开机自启动某服务
    如：
    systemctl restart nginx
```

- 增加用户组和用户：

```

    useradd -g testgroup testuser
```

# Nginx
使用你喜欢的shell工具，连接上Linux服务器，比如XShell，[点此下载](http://blog-resource.bj.bcebos.com/files/2016/06/Xshell5.exe)。首先，安装Nginx服务器，这里我们不讨论Apache和Nginx，以后有机会再深入学习。我的服务器安装的是CentOS系统，其他的Ubuntu安装原理也是一样。

- 安装

执行以下指令安装Nginx：

```yum install nginx```

- 配置

使用```nginx -v```指令检查Nginx是否安装完成。然后打开Nginx配置文件：

```

    vi /etc/nginx/nginx.conf
```

```

    server {
        listen       80 default_server;  //监听端口
        listen       [::]:80 default_server;
        server_name  blog.codingplayboy.com;  //服务器访问地址
        root         /usr/share/nginx/html;  //服务器根目录

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {  //服务器根目录访问映射路径
            root /usr/share/nginx/html/blog-wp;
            index index.php index.html index.htm;  //首页
        }

        error_page 404 /404.html;
            location = /40x.html {
            root /usr/share/nginx/html;
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
            root /usr/share/nginx/html;
        }

        rewrite /wp-admin$ $scheme://$host$uri/ permanent;
        location ~ \.php$ {  //解析访问php文件时处理规则
            root /usr/share/nginx/html/blog-wp;
            fastcgi_pass 127.0.0.1:9000;  //php-fpm
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
        }
    }
```

- 权限设置

访问服务器地址，会出现Access Denied，403Forbidden禁止访问，我们还需要修改服务器访问目录权限

```

    chown -R nginx:nginx /usr/share/nginx/html/blog-wp
    chmod -R 774 /usr/share/nginx/html/blog-wp
```

nginx:nginx为Linux用户和用户组，后文配置php-fpm时将创建。

- SELinux设置

为了能完整使用wordpress读写博客、安装主题插件功能还需要修改SELinux安全上下文以拥有对目录文件的读写权限：

```

    setsebool -P httpd_read_user_content 1
    setsebool -P httpd_enable_homedirs 1
    chcon -R -t httpd_sys_rw_content_t /usr/share/nginx/html/blog-wp
```

## MySQL

- 安装

CentOS7以前可以安装MySQL，但是在CentOS7中MySQL由MariaDB替代，大致还是相同的安装方式：

```

    yum install mariadb mariadb-server   
    或
    yum install mysql mysql-server
```

- 配置

安装完成后，拷贝配置文件至/etc/my.conf，若已存在my.conf直接覆盖：

```

    cp /usr/share/mysql/my-huge.cnf /etc/my.cnf
```

- 创建数据库

随后给数据库创建用户，输入指令：

```

    mysql_secure_installation
```
按照提醒依次输入用户名、密码，对于其他询问可以直接确定，到最后创建账户成功。

```

    mysql -u root -p
```输入之前创建的用户名和密码登录数据库，创建一个wordpress数据库，数据库名可以自定义:

```

    create database wordpress
```

```use wordpress```切换至该数据库，创建连接该数据库的账号：

```

    grant all on wordpress.* to username identified by "password"
```

- 权限

修改数据库文件权限：

```

    chown mysql:mysql /var/lib/mysql -R
```

## PHP
 
- 安装

```
 
    sudo yum -y install php php-fpm php-mysql php-gd php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring php-snmp php-soap curl
```
 
```
 
    sudo yum -y install epel-release
```
 
- 配置

打开php-fpm配置文件：
 
```
 
    vi /etc/php-fpm.d/www.conf
```
 
 找到对应owner,user,group，修改为nginx用户和nginx用户组：
 
```
 
    listen.owner = nginx
    listen.group = nginx
    listen.mode = 0666
    ...
    user = nginx
    group = nginx

```
 然后激活nginx用户：
 
```
 
    usermod -s /bin/bash username
```也可以直接修改文件：
 
```
    cat /etc/passwd
```
发现它的shell是“/sbin /nologin”，需要将起改成“/bin/bash”。

- 配置php.ini

 打开php.ini配置文件：
 
```
 
    vi /etc/php.ini
```
 找到open_basedir所在行，此参数规定php所能运行的目录，若不修改该值，访问nginx服务器将报Access Denied错误；将该参数值修改为服务器访问目录或者直接注释该行。

- 启动

 启动php-fpm:
 
```
 
    systemctl start php-fpm
    systemctl enable php-fpm    
    //设置开机自启动php-fpm
```
 
## WordPress
 
 下载WordPress安装包，可以直接wget获取也可以ftp上传，解压到/usr/share/nginx/html/blog-wp，访问index.php即进行安装：
 
![wordpress五分钟安装](http://blog-resource.bj.bcebos.com/photos/2016/06/wpset1.png)

点击开始安装：

![wordpress安装](http://blog-resource.bj.bcebos.com/photos/2016/06/wpset2.png)

安装好后就可以正常访问了，开始安装wordpress插件可能会提示让你连接FTP:

![连接FTP](http://blog-resource.bj.bcebos.com/photos/2016/06/wpimport.png)，如果有你可以连接，但是也可以直接修改之前安装wordpress时生产的wp-config.php配置文件,在文件添加如下代码：

```

    define('FS_METHOD', 'direct');
    define('FS_CHMOD_DIR', 0777);
    define('FS_CHMOD_FILE', 0777);
```

再次安装即可正常安装成功，接下来开始你的第一篇博客吧。
 
 