#  源码编译安装php

## 资料

| 名称                                 | 地址                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| Centos 7源码编译安装 php7.2 之生产篇 | [link](https://www.linuxprobe.com/centos7-install-php72.html) |
| php学院-CentOS 7.4 编译安装 PHP7     | [link](https://www.php.cn/topic/php7/449121.html)            |
| php 国内官网                         | [link](http://php.p2hp.com/)  [link](https://www.p2hp.com/)  |
| php-搜狐镜像                         | [link](http://mirrors.sohu.com/php/)                         |
| 第三方博客参考                       | [link](https://cloud.tencent.com/developer/article/1436889?from=15425) |

##  [下载php7源码]

> 进入php[官网](https://www.php.net/),点击[Downloads](https://www.php.net/downloads)

![1636332939(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/1rABOpzM3haU8WQ.png)

**安装编译工具及库文件**

```shell
yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel autoconf automake
```

###  linux-wget下载php7

> --no-check-certificate 不校验证书

```shell
wget https://www.php.net/distributions/php-7.4.3.tar.gz --no-check-certificate
```

##  php7 源码编译安装

### 解压 && 进入解压目录

```shell
# 解压&&进入
tar -zxvf php-7.4.3.tar.gz && cd php-7.4.3/
```

###   configure

| 名称                       | 地址                                                      |
| -------------------------- | --------------------------------------------------------- |
| configure-核心配置选项列表 | [link](https://www.php.net/manual/zh/configure.about.php) |
|                            |                                                           |

```php
# 安装php依赖
yum -y install libxml2-devel sqlite-devel
# ./configure --prefix=/home/.... ,--perfix参数指定目录 此参数可以不加 编译 PHP 时需要 --enable-fpm 配置选项来激活 FPM 支持。
# 编译fpm支持 https://www.php.net/manual/zh/install.fpm.install.php
#关联的扩展都在 源码的/root/php-7.4.3/ext 目录下
/**
 * 这里注意--disable-fileinfo 扩展内存小例如虚拟机安装docker安装php的时候很难编译成功所以禁用
 */
[root@VM-56-0-centos php-7.4.3]#  ./configure  --with-openssl --with-phar  --with-c   --prefix=/usr/local/php7.4.3  --with-config-file-path=/usr/local/php7.4.3/etc  --disable-fileinfo  --enable-fpm --without-pear  --disable-phar 

/**
*  备注
*  ./configure  --with-openssl --with-phar   --prefix=/usr/local/php7.4.3  --with-config-file-path=/usr/local/php7.4.3/etc  --disable-    *  fileinfo  --enable-fpm --without-pear  --disable-phar 
*/

# 出现以下则代表成功
+--------------------------------------------------------------------+
| License:                                                           |
| This software is subject to the PHP License, available in this     |
| distribution in the file LICENSE. By continuing this installation  |
| process, you are bound by the terms of this license agreement.     |
| If you do not agree with the terms of this license, you must abort |
| the installation process at this point.                            |
+--------------------------------------------------------------------+

Thank you for using PHP.
```

编译遇到bug

1. libxml 缺少提示

> No package 'libxml-2.0' found
>
> checking whether to build with LIBXML support... yes
> checking for libxml-2.0 >= 2.7.6... no
> configure: error: Package requirements (libxml-2.0 >= 2.7.6) were not met:
>
> No package 'libxml-2.0' found

```shell
yum -y install libxml2-devel
```

2. sqlite3缺少提示

> checking for sqlite3 > 3.7.4... no
> configure: error: Package requirements (sqlite3 > 3.7.4) were not met:
>
> No package 'sqlite3' found

```shell
yum -y install sqlite-devel
```

3. make: *** [ext/fileinfo/libmagic/apprentice.lo] Error 1

> 原因是由于服务器内存不足1G，跑不动。
>
> 在./configure 时加–disable-fileinfo

```shell
./configure --disable-fileinfo --with-php-config=/usr/local/php/bin/php-config 
```

###  make && make install

```shell
# 编译
[root@VM-64-25-centos php-7.4.3]# make && make install 
```

**测试命令**

```shell
# 只有在 /usr/local/php7.4.3/bin 目录下执行命令
[root@VM-64-25-centos bin]# /usr/local/php7.4.3/bin/php --version
PHP 7.4.3 (cli) (built: Nov  9 2021 08:24:01) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
```

###  建立全局php命令

```shell
ln -s /usr/local/php7.4.3/bin/php  /usr/bin/php # 然后你就可以在任何地方使用php 命令了(推荐使用)

# 其它方式

# 第一种方式
1. 打开指令
vim ~/.bash_profile
2. 加入路径
alias php=/usr/local/php7.4.3/bin/php
3. 写入系统
source ~/.bash_profile
另外一种方法

# 第二种方式
1. 打开全局指令集
vim /etc/profile
2. 加入php指令到最后面
PATH=$PATH:/usr/local/php7.4.3/bin/php
export PATH
3. 写入系统
source /etc/profile

# 测试 
[root@VM-64-25-centos /]# php -v
PHP 7.4.3 (cli) (built: Nov  9 2021 08:24:01) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
```

复制php.ini

```shell
# 查询php.ini位置(后面赋值的php.inib)
[root@VM-139-48-centos include]# php -r "phpinfo();" | grep 'php.ini'   
Configuration File (php.ini) Path => /usr/local/php7.4.3/lib
# or 
[root@VM-139-48-centos ~]# find / -name php.ini*
/root/php-7.4.3/php.ini-development
/root/php-7.4.3/php.ini-production

# 如果出现了以下的情况找不到（php.ini）
[root@VM-139-48-centos ~]# php --ini
Configuration File (php.ini) Path: /usr/local/php7.4.3/lib
Loaded Configuration File:         (none)
Scan for additional .ini files in: (none)
Additional .ini files parsed:      (none)

# 可以在源码那里复制过来
cp /root/php-7.4.3/php.ini-development /usr/local/php7.4.3/lib/php.ini
```

###  启动php-fpm

> 编译的时候加上**--enable-fpm**

```shell
# 首先启动报错 错误信息说是找不到php-fpm.conf
[root@101fe8e80378 /]# /usr/local/php7.4.3/sbin/php-fpm 
[17-Nov-2021 08:10:54] ERROR: failed to open configuration file '/usr/local/php7.4.3/etc/php-fpm.conf': No such file or directory (2)
[17-Nov-2021 08:10:54] ERROR: failed to load configuration file '/usr/local/php7.4.3/etc/php-fpm.conf'
[17-Nov-2021 08:10:54] ERROR: FPM initialization failed
# cp 一份 php-fpm.conf
[root@101fe8e80378 /]# cd /usr/local/php7.4.3/etc/
[root@101fe8e80378 etc]# ls
php-fpm.conf.default  php-fpm.d
[root@101fe8e80378 etc]# cp php-fpm.conf.default  php-fpm.conf
[root@101fe8e80378 etc]# ls
php-fpm.conf  php-fpm.conf.default  php-fpm.d
# 编辑 php-fpm.conf 大概在17行取消注释
pid = run/php-fpm.pid
 
# 如果报错
[root@101fe8e80378 etc]# /usr/local/php7.4.3/sbin/php-fpm
[17-Nov-2021 08:24:11] WARNING: Nothing matches the include pattern '/usr/local/php7.4.3/etc/php-fpm.d/*.conf' from /usr/local/php7.4.3/etc/php-fpm.conf at line 143.
[17-Nov-2021 08:24:11] ERROR: No pool defined. at least one pool section must be specified in config file
[17-Nov-2021 08:24:11] ERROR: failed to post process the configuration
[17-Nov-2021 08:24:11] ERROR: FPM initialization failed
# 操作如下
[root@101fe8e80378]#  cd /usr/local/php7.4.3/etc/php-fpm.d
[root@101fe8e80378 php-fpm.d]# cp www.conf.default www.conf
# 再次启动没有报错即可
[root@101fe8e80378 php-fpm.d]# /usr/local/php7.4.3/sbin/php-fpm

# 查看是否启动成功
[root@101fe8e80378 php-fpm.d]# netstat -ntlp | grep 9000
tcp        0      0 127.0.0.1:9000          0.0.0.0:*               LISTEN      183277/php-fpm: mas 
```

**相关nginx配置**

```shell
server {
    listen 80;
    server_name www.cs.com;
    root /work/www/laravel/public/;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.php;
    client_max_body_size 100m;
    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        # fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_pass  127.0.0.1:9000;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```



## 扩展使用dockerfile编译安装

> 十分不推荐啊，这个亲测构建需要二十多分钟，构建出来的镜像有1.29个G,吐血了都

```shell
FROM centos:latest
# 设置作者信息
MAINTAINER yaoliuyang<762301880@qq.com>
#  安装vim编辑器&编译工具
RUN yum -y  update \
  && yum -y install vim \
     wget \
  && yum -y install make \
       zlib \
       zlib-devel \
       gcc-c++  \
       libtool \
       openssl \
       openssl-devel
# 安装php 跳过校验整数下载
# 安装依赖
RUN yum -y install libxml2-devel \
     sqlite-devel \
      # 下载源码包
      && wget https://www.php.net/distributions/php-7.4.3.tar.gz --no-check-certificate \
      # 解压 & 进入
      && tar -zxvf  php-7.4.3.tar.gz \
      && cd php-7.4.3 \
      # 配置  --disable-fileinfo
      && ./configure  --prefix=/usr/local/php7.4.3   --disable-fileinfo  --enable-fpm --without-pear  --disable-phar \
      && make \
      && make install \
      && ln -s /usr/local/php7.4.3/bin/php  /usr/bin/php \
      && cp /php-7.4.3/php.ini-development /usr/local/php7.4.3/lib/php.ini \
      && rm -rf /php-7.4.3 \
      && rm -rf /php-7.4.3.tar.gz \
```

**构建dockerfile**

```shell
docker build -t myphp:0.1 .
```

#   不重新编译php的情况下 添加扩展

## 说明

> 源码编译的php发现没有openssl扩展不想重新编译感觉很麻烦

## 参考

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://blog.csdn.net/weixin_42392367/article/details/115564198) |
|            |                                                              |

## 方案

## 解压对应php版本的源码包

> 进入到想要的扩展目录

```shell
cd /root/php-7.4.3/ext/openssl

[root@101fe8e80378 openssl]# /usr/local/php7.4.3/bin/phpize
Cannot find config.m4. 
Make sure that you run '/usr/local/php7.4.3/bin/phpize' in the top level source directory of the module
# 复制一份
[root@101fe8e80378 openssl]# cp config0.m4 config.m4
# 执行编译
[root@101fe8e80378 openssl]# /usr/local/php7.4.3/bin/phpize
Configuring for:
PHP Api Version:         20190902
Zend Module Api No:      20190902
Zend Extension Api No:   320190902
# 执行编译
[root@101fe8e80378 openssl]# ./configure --with-openssl --with-php-config=/usr/local/php7.4.3/bin/php-config 
[root@101fe8e80378 openssl]# make && make install

# 再php.ini中添加扩展即可-此时虽然可以看见扩展但是并没有生效
extension=openssl.so

# 最后需要重启服务器才能生效
[root@15fb15a92f7c no-debug-non-zts-20190902]# netstat -anp | grep 9000
tcp        0      0 127.0.0.1:9000          0.0.0.0:*               LISTEN      5289/php-fpm: maste
[root@15fb15a92f7c no-debug-non-zts-20190902]# kill 5289
/usr/local/php7.4.3/sbin/php-fpm # 重启php

```

## 常用扩展安装

 ## curl扩展

**1.进入ext/curl目录**

```shell
# 查询phpize位置
[root@15fb15a92f7c curl]# find / -name phpize
/usr/local/php7.4.3/bin/phpize
# 编译
[root@15fb15a92f7c curl]# /usr/local/php7.4.3/bin/phpize
Configuring for:
PHP Api Version:         20190902
Zend Module Api No:      20190902
Zend Extension Api No:   320190902
# 配置
[root@15fb15a92f7c curl]# ./configure --with-curl --with-php-config=/usr/local/php7.4.3/bin/php-config 
........
# 安装
[root@15fb15a92f7c curl]# make && make install 
# 安装成功展示
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------
Build complete.
Don't forget to run 'make test'.
Installing shared extensions:     /usr/local/php7.4.3/lib/php/extensions/no-debug-non-zts-20190902/ # 这个就是扩展文件的位置
# 配置php.ini  
[root@15fb15a92f7c /]# find / -name php.ini
/usr/local/php7.4.3/etc/php.ini
[root@15fb15a92f7c /]# vim /usr/local/php7.4.3/etc/php.ini
....
extension=curl  # 找到这个位置然后取消注释
....
# 最后重启php-fpm
```

**2.报错解决**

```shell
# 如果报错 需要安装 yum install libcurl-devel 
checking for libcurl >= 7.15.5... no
configure: error: Package requirements (libcurl >= 7.15.5) were not met:

Package 'libcurl', required by 'virtual:world', not found

Consider adjusting the PKG_CONFIG_PATH environment variable if you
installed software in a non-standard prefix.

Alternatively, you may set the environment variables CURL_CFLAGS
and CURL_LIBS to avoid the need to call pkg-config.
See the pkg-config man page for more details.
# 安装libcurl-devel扩展
yum -y install libcurl-devel 
```

**gd扩展**

> gd库依赖**zlib**  **libpng**           
>
> **zlib安装**
>
> >  No package 'zlib' found    
>
> 对于 Ubuntu/Debian 系统
>
> apt-get update
> apt-get install zlib1g-dev
>
> 对于 CentOS/RHEL 系统：
>
> yum install zlib-devel
>
> 安装完 zlib 库后，再尝试执行 `docker-php-ext-install gd` 命令，应该可以成功安装 GD 扩展了。
>
> **libpng**安装
>
> >    No package 'libpng' found
>
> 对于 Ubuntu/Debian 系统：
>
> apt-get update
> apt-get install libpng-dev
>
> 对于 CentOS/RHEL 系统：
>
> yum install libpng-devel

```shell
./configure  --with-php-config=/usr/local/php7.4.3/bin/php-config
# 提示报错
Package 'libpng', required by 'virtual:world', not found # 报错提示
# 安装
yum -y install libpng-devel

./configure  --with-php-config=/usr/local/php7.4.3/bin/php-config
make && make install
```

**这里安装的是不完整的gd扩展**

![1640659364(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/E6d9hbyYW8vfiFD.png)

**执行命令的时候会报以下错误**

```shell
ErrorException
imagecreatefromstring(): No JPEG support in this PHP build
```

**解决方案**

```shell
[root@15fb15a92f7c gd]# pwd
/root/php-7.4.3/ext/gd
# 查看安装帮助 会看到如下一条命令
[root@15fb15a92f7c gd]# ./configure -h
****
--with-jpeg             GD: Enable JPEG support (only for bundled libgd)
****

yum -y install libjpeg-devel

# m
#wget http://www.ijg.org/files/jpegsrc.v9d.tar.gz
#tar -zxvf jpegsrc.v9d.tar.gz && cd jpeg-9d/
#./configure --prefix=/usr/local/libjpeg
#make && make install
```





