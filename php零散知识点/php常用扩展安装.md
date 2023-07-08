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