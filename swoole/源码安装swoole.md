











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

# 日后补充

