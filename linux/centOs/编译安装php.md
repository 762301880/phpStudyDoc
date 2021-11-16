#  源码编译安装php

## 资料

| 名称                                 | 地址                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| Centos 7源码编译安装 php7.2 之生产篇 | [link](https://www.linuxprobe.com/centos7-install-php72.html) |
| php学院-CentOS 7.4 编译安装 PHP7     | [link](https://www.php.cn/topic/php7/449121.html)            |
| php 国内官网                         | [link](http://php.p2hp.com/)  [link](https://www.p2hp.com/)  |
| php国内镜像                          | [link](http://mirrors.sohu.com/php/)                         |

##  [下载php7源码]

> 进入php[官网](https://www.php.net/),点击[Downloads](https://www.php.net/downloads)

![1636332939(1).jpg](https://i.loli.net/2021/11/08/1rABOpzM3haU8WQ.png)

**安装编译工具及库文件**

```shell
yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-deve
```

###  linux-wget下载php7

> --no-check-certificate 不校验证书

```shell
wget https://www.php.net/distributions/php-7.4.3.tar.gz --no-check-certificate
```

##  php7 源码编译安装

### 解压 && 进入解压目录

```shell
# 解压
tar -zxvf php-7.4.3.tar.gz
# 进入上一部解压的php目录
cd php-7.4.3/
```

###   configure

```shell
# 安装php依赖
yum -y install libxml2-devel sqlite-devel
# ./configure --prefix=/home/.... ,--perfix参数指定目录 此参数可以不加 编译 PHP 时需要 --enable-fpm 配置选项来激活 FPM 支持。
# 编译fpm支持 https://www.php.net/manual/zh/install.fpm.install.php
[root@VM-56-0-centos php-7.4.3]#  ./configure  --prefix=/usr/local/php7.4.3  --disable-fileinfo  --enable-fpm --without-pear  --disable-phar 

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
# 查询php.ini位置
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
      && ./configure  --prefix=/usr/local/php7.4.3  --disable-fileinfo  --enable-fpm --without-pear  --disable-phar \
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

