##  	

# 介绍

> `Swoole` 是一个使用 `C++` 语言编写的基于异步事件驱动和协程的并行网络通信引擎，为 `PHP` 提供[协程](https://wiki.swoole.com/#/coroutine)、[高性能](https://wiki.swoole.com/#/question/use?id=swoole性能如何)网络编程支持。提供了多种通信协议的网络服务器和客户端模块，可以方便快速的实现 `TCP/UDP服务`、`高性能Web`、`WebSocket服务`、`物联网`、`实时通讯`、`游戏`、`微服务`等，使 `PHP` 不再局限于传统的 Web 领域。

## 参考资料

| name              | address                                          |
| ----------------- | ------------------------------------------------ |
| swoole官网        | [点我跳转](https://wiki.swoole.com/#/)           |
| swoole-github下载 | [点我跳转](https://github.com/swoole/swoole-src) |

# 安装

## 使用`wget`安装

```shell
wget https://pecl.php.net/get/swoole-4.6.6.tgz 
```

## 检查依赖

- 此段内容截取至[博客](https://www.cnblogs.com/gyfluck/p/11275114.html)

```shell
  #检查一下环境和依赖要求，查看swoole官网：https://wiki.swoole.com/。

　　#查看依赖，https://wiki.swoole.com/wiki/page/7.html。
　　#查看编译安装的准备：https://wiki.swoole.com/wiki/page/6.html。
　　#安装前必须保证系统已经安装了下列软件
　　　　php-7.0 或更高版本
　　　　gcc-4.8 或更高版本
　　　　make
　　　　autoconf
　　　　pcre (CentOS系统可以执行命令：yum install pcre-devel)
　　#检查命令：
　　php -v 查看php版本
　　gcc -v 查看gcc版本
　　rpm -qa make 检查是否安装make。
　　rpm -qa autoconf 检查是否安装autoconf。
　　rpm -qa pcre 检查是否安装pcre。
```

## 安装依赖

```shell
yum -y install gcc gcc-c++ autoconf pcre-devel make
```

## 编译 

```shell
phpize
./configure 
make
make install
```



  ## 安装swoole

- 解压安装包

```shell
tar -zxvf swoole-src-4.6.6
```



- 在gitHub上下载swoole压缩包并上传到服务器中

```shell
cd swoole-src-4.6.6 # 进入已解压的swoole
```



## 在php中配置swoole

- 查询php.ini 的位置

```shell
find / -name php.ini # 使用find命令查询文件的位置
```

- 在php.ini中添加配置

```shell
extension=swoole.so
```

4555

- 查看安装的扩展

```shell
php -m
```

