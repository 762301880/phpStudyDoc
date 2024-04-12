## ubuntu-apt命令安装常用扩展

> 如果php是对应版本的后面也要加上对应的版本号例如php7-curl

```shell
apt -y install  php-mysql php-gd php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring  php-soap curl php-curl 
```



# 编译安装扩展

## **gd库安装**

### zlib安装

> **gd库依赖 zlib libpng**

这个错误通常是由于缺少 zlib 库导致的。如果你在 Docker 容器中安装 GD 扩展时遇到了这个问题，你需要确保先安装 zlib 库以解决该错误。

以下是在常见 Linux 发行版中安装 zlib 库的示例命令：

对于 Ubuntu/Debian 系统：

```shell
apt-get update
apt-get install zlib1g-dev
```

对于 CentOS/RHEL 系统：

```shell
yum install zlib-devel
```

### **libpng 安装**

这个错误表明在你的系统中找不到 libpng 库。libpng 库是 GD 扩展所需的依赖项之一。

你可以尝试以下命令来安装 libpng 库：

对于 Ubuntu/Debian 系统：

```shell
apt-get update
apt-get install libpng-dev
```

对于 CentOS/RHEL 系统：

```shell
yum install libpng-devel
```

安装完成后，再次尝试执行 `docker-php-ext-install gd` 命令，应该可以成功安装 GD 扩展了。如果还有其他依赖项缺失，按照错误提示一一解决即可。

# amqp安装

## windows安装

**下载地址**

| name             | url                                                          |
| ---------------- | ------------------------------------------------------------ |
| pecl下载         | [link](https://pecl.php.net/package/amqp)                    |
| 安装扩展博客参考 | [link](https://blog.csdn.net/github_37468379/article/details/108726880) |

**点击dll下载版本***

![image-20230923093413519](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20230923093413519.png)

**下载对应版本**

>  **是否线程安全可以打印phpinfo()**查询线程是否安全
>
>  **Thread Safety**  值为**disabled** 就是代表**你的PHP版本不支持线程安全**
>
>  本人下载的是**Non Thread Safe**

![image-20230811095220357](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20230811095220357.png)

**点击下载对应版本**

![image-20230923093318512](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20230923093318512.png)

**解压后复制其中的 rabbitmq.dll**

> 从官方PECL amqp页面下载适用于您的php的正确软件包
> https://pecl.php.net/package/amqp
> 我的php版本是7.4.3，我下载的包是amqp 1.9.4 for Windows-7.3 Thread Safe (TS) x64
> 添加php_amqp.dll到您的php ext文件夹并启用php.ini文件中的扩展名：extension=php_amqp.dll
> 如果是32位系统，请将rabbitmq.4.dll复制到C:/Windows/System32中。如果使用64位系统，请将其添加到C:/Windows/SysWOW64和C:/Windows/System32文件夹中。
> 重新启动Apache
>
> 

![image-20230923112408371](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20230923112408371.png)



![image-20230923094423288](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20230923094423288.png)

**修改php.ini**配置文件

> 添加以下配置

```php
[amqp]
extension=php_amqp.dll
```

