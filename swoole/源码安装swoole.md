











#  一、源码编译安装php

## 1.1 下载php7源码

### 1.1.1 进入php[官网](https://www.php.net/),点击[Downloads](https://www.php.net/downloads)

```php
[php-7.4.21.tar.gz][(sig)]
(https://www.php.net/distributions/php-7.4.21.tar.gz.asc) [16,223Kb]
```

### 1.1.2 linux-wget下载php7

```shell
    wget https://www.php.net/distributions/php-7.4.21.tar.gz
```

## 1.2 php7 源码编译安装

### 1.2.1 解压

```shell
tar -zxvf https://www.php.net/distributions/php-7.4.21.tar.gz
```

###  1.2.2 configure

```shell
# 进入上一部解压的php目录
yaoliuyang@benben:~ $ cd php-7.4.21/
# 编译
yaoliuyang@benben:~/php-7.4.21$ ./configure    #./configure --perfix=/home/.... ,--perfix参数指定目录 此参数可以不加
```

### 1.2.3 make

```shell
# 编译
yaoliuyang@benben:~/php-7.4.21$ make
```

### 1.2.4 make install

```shell
yaoliuyang@benben:~/php-7.4.21$ make install
```

## 二 、[源码编译安装swoole](https://wiki.swoole.com/wiki/page/6.html)

```shell
安装前必须保证系统已经安装了下列软件

php-7.0 或更高版本
gcc-4.8 或更高版本
g++
make
autoconf
pcre (CentOS系统可以执行命令：yum install pcre-devel)
```

**资料**

| 名称               | 地址                                          |
| ------------------ | --------------------------------------------- |
| swoole官网安装文档 | [link](https://wiki.swoole.com/#/environment) |
|                    |                                               |

**下载swoole源码**

> 下载swoole的源码包

```shell
wget https://pecl.php.net/get/swoole-4.8.1.tgz
```

源码包地址

```shell
https://github.com/swoole/swoole-src/releases  # github
https://pecl.php.net/package/swoole            # php
https://gitee.com/swoole/swoole/tags           # gitee 
```

**解压源码包**

```shell
tar -zxvf swoole-4.8.1.tgz
```

**如果提示没有phpize**

> 使用phpize来生成php编译配置

```shell
sudo apt-get install php-dev # 来安装 phpize
```

**编译安装**

> *./configure* 来做编译配置检测
>
> make进行编译
>
> make install进行安装

```shell
cd swoole-4.8.1 && \
phpize && \
./configure && \
make && sudo make install
```

- 如果报错

```shell
make: *** 没有指明目标并且找不到 makefile。 停止。#  此时需要检查一下有没有安装 gcc g++
```



**启用扩展**

```shell
编译安装到系统成功后，需要在 php.ini 中加入一行 extension=swoole.so 来启用 Swoole 扩展
```

**查看是否安装成功**

```shell
php -m 
...
swoole # 显示出的扩展有swoole即可
...
```

