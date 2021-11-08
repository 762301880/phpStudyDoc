#  源码编译安装php

##  [下载php7源码]

> 进入php[官网](https://www.php.net/),点击[Downloads](https://www.php.net/downloads)

![1636332939(1).jpg](https://i.loli.net/2021/11/08/1rABOpzM3haU8WQ.png)



###  linux-wget下载php7

> --no-check-certificate 不校验证书

```shell
wget https://www.php.net/distributions/php-7.4.21.tar.gz --no-check-certificate
```

##  php7 源码编译安装

### 解压

```shell
tar -zxvf php-7.4.21.tar.gz
```

###   configure

```shell
# 进入上一部解压的php目录
yaoliuyang@benben:~ $ cd php-7.4.21/
# ./configure --prefix=/home/.... ,--perfix参数指定目录 此参数可以不加 编译 PHP 时需要 --enable-fpm 配置选项来激活 FPM 支持。
# 编译fpm支持 https://www.php.net/manual/zh/install.fpm.install.php
yaoliuyang@benben:~/php-7.4.21$ ./configure  --prefix=/usr/local/php7.4.21  --disable-fileinfo  --enable-fpm
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

###  make

```shell
# 编译
yaoliuyang@benben:~/php-7.4.21$ make
```

###  make install

```shell
yaoliuyang@benben:~/php-7.4.21$ make install
```

###  建立全局php命令

```shell
ln -s /usr/local/php7.4.21/bin/php  /usr/bin/php # 然后你就可以在任何地方使用php 命令了
```

复制php.ini

```shell
# 查询php.ini位置
[root@VM-139-48-centos include]# php -r "phpinfo();" | grep 'php.ini' 
Configuration File (php.ini) Path => /usr/local/php7.4.21/lib
# or 
[root@VM-139-48-centos ~]# find / -name php.ini*
/root/php-7.4.21/php.ini-development
/root/php-7.4.21/php.ini-production

# 如果出现了以下的情况找不到（php.ini）
[root@VM-139-48-centos ~]# php --ini
Configuration File (php.ini) Path: /usr/local/php7.4.21/lib
Loaded Configuration File:         (none)
Scan for additional .ini files in: (none)
Additional .ini files parsed:      (none)

# 可以在源码那里复制过来
cp /root/php-7.4.21/php.ini-development /usr/local/php7.4.21/lib/php.ini
```

