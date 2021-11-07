











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

**资料**

| 名称         | 地址                                          |
| ------------ | --------------------------------------------- |
| 官网安装文档 | [link](https://wiki.swoole.com/#/environment) |

## 编译安装

**官网介绍**

>`Swoole` 扩展是按照 `PHP` 标准扩展构建的。使用 `phpize` 来生成编译检测脚本，`./configure` 来做编译配置检测，`make` 进行编译，`make install` 进行安装。
>
>- **如无特殊需求，请务必编译安装 `Swoole` 的最新 [release](https://github.com/swoole/swoole-src/releases/latest) 版本或 [v4.4LTS](https://github.com/swoole/swoole-src/tree/v4.4.x)**
>- 如果当前用户不是 `root`，可能没有 `PHP` 安装目录的写权限，安装时需要 `sudo` 或者 `su`
>- 如果是在 `git` 分支上直接 `git pull` 更新代码，重新编译前务必要执行 `make clean`
>- 仅支持 `Linux`(2.3.32 以上内核)、`FreeBSD`、`MacOS` 三种操作系统，低版本 Linux 系统（如 `CentOS 6`）可以使用 `RedHat` 提供的 `devtools` 编译，[参考文档](https://blog.csdn.net/ppdouble/article/details/52894271)， 在 `Windows` 平台，可使用 `WSL(Windows Subsystem for Linux)` 或 `CygWin`
>- 部分扩展与 `Swoole` 扩展不兼容，参考[扩展冲突](https://wiki.swoole.com/#/getting_started/extension)

**必须安装的编译软件**

```shell
# 安装前必须保证系统已经安装了下列软件 除了php 却啥装啥 例如: sudo  apt install -y gcc g++ make autoconf 

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

## pecl安装

**资料**

| 资料名称                               | 资料地址                                                 |
| -------------------------------------- | -------------------------------------------------------- |
| `laravel`学院`Swoole` 从入门到实战教程 | [地址](https://laravelacademy.org/books/swoole-tutorial) |
|                                        |                                                          |

`Ubuntu安装swoole`

- 参考[资料](https://laravelacademy.org/post/9780)

如果是在服务器安装的话，以` Ubuntu` 系统为例，通过执行下列命令安装即可：

```shell
sudo pecl install swoole
```

- 如果提示 `Command 'pecl' not found, but can be installed with:`

```shell
sudo apt install php7.4-dev    # 必须要安装用于执行phpize来生成编译检测脚本 
sudo apt install php-pear      # 必须安装用于直接安装swoole的命令
```

- 然后通过 `php -i | grep php.ini` 定位 `php.ini` 文件所在位置，并打开该配置文件，在文件末尾追加如下内容：

```shell
[swoole]
extension=swoole.so
```

- 保存并退出，在终端运行 `php -m`，如果看到扩展里包含 `swoole`，说明安装启用成功。

