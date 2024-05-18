# 说明

> 源码编译的php发现没有openssl扩展不想重新编译感觉很麻烦

## 参考

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://blog.csdn.net/weixin_42392367/article/details/115564198) |
|            |                                                              |

# 方案

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

# 常用扩展安装

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





