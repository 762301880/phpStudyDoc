# mysql 安装 

说明

> 本来之前使用的是Ubuntu系统结果无缘无故的崩了(已经是第二次了，虽然这次只是开个机)，
>
> deepin自带的源没有mysql-server，还好网络上有很多解决方案

## 资料

| 名称                              | 地址                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| 第三方博客-Deepin V20 安装MySQL 8 | [link](https://bbs.deepin.org/post/191965#mod=viewthread&tid=191965&extra=) |
| 知乎deepin系统使用体验            | [link](https://zhuanlan.zhihu.com/p/66428353)                |

## 安装

[官网下载mysql存储库安装](https://bbs.deepin.org/post/191965#mod=viewthread&tid=191965&extra=)

### [**下载存储库**](https://dev.mysql.com/downloads/repo/apt/)

> 打开mysql官网[点我跳转]下载(https://dev.mysql.com/downloads/repo/apt/)	

<img src="https://i.loli.net/2021/11/01/okujFMV4hylmqiA.png" alt="1635726424(1).jpg" style="zoom: 50%;" />

<img src="https://i.loli.net/2021/11/01/TlvwtHidM3nzs7Y.png" alt="1635726527(1).jpg" style="zoom:50%;" />

或者直接在终端使用命令下载

```shell
wget https://dev.mysql.com/get/mysql-apt-config_0.8.20-1_all.deb
```

### 安装存储库

> 在下载的deb包目录中打开终端使用dpkg命令安装deb包

```shell
sudo dpkg -i mysql-apt-config_0.8.20-1_all.deb
```

### 刷新软件包

```shell
sudo apt update
```

### 执行安装

```shell
# 选择完版本之后执行 即可
sudo apt-get install mysql-server 
```

**过程会提示输入root用户新密码**

![fa89610d55b76283e2faf52c49c4fc8.png](https://i.loli.net/2021/11/01/pznmwYJgo2DsljA.png)

**校验新密码**

![ee6f57d83825d9007e2c9b98a9be6e5.png](https://i.loli.net/2021/11/01/rnVxa1GcW6jhDBy.png)
**安装完成之后直接使用即可**

> 登录用户并输入密码

```shell
root@2c546338dc3e:~# mysql -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 43
Server version: 5.5.53-0+deb8u1 (Debian)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
3 rows in set (0.01 sec)

mysql>

```

## mariadb-替代mysql使用(不推荐)

> 由于**deepin**      apt源里没有mysql-server所以可以考虑使用**mariadb**
>
> **介绍**
>
> MariaDB数据库管理系统是MySQL的一个分支，主要由开源社区在维护，采用GPL授权许可 MariaDB的目的是完全兼容MySQL，包括API和命令行，使之能轻松成为MySQL的代替品。在存储引擎方面，使用XtraDB（英语：XtraDB）来代替MySQL的InnoDB。 MariaDB由MySQL的创始人Michael Widenius（英语：Michael Widenius）主导开发，他早前曾以10亿美元的价格，将自己创建的公司MySQL AB卖给了SUN，此后，随着SUN被甲骨文收购，MySQL的所有权也落入Oracle的手中。MariaDB名称来自Michael Widenius的女儿Maria的名字。

```shell
#  apt 安装
sudo apt-get install mariadb-server mariadb-client

# 安装后，默认的密码是空，通过以下命令进入数据库

sudo mysql -uroot -p

# 提示要输入数据库密码直接回车，我们接下来去修改root用户的密码

# 执行以下SQL语句

use mysql;

updateuser set plugin="mysql_native_password",authentication_string=password('新密码') where user="root";

FLUSHPRIVILEGES;

# 然后断开连接，之后我们区设置开机自启动

sudo systemctl enable mysqld.service

sudo systemctl daemon-reload

# 这整个安装过程就完毕了！
```

# php 安装

```shell
sudo apt update --fix-missing # 更新镜像源
sudo apt -y install php7.4-fpm # 不推荐因为deepin 默认最高php版本是7.3
sudo apt install php #推荐
# 安装默认常用php 扩展
apt -y install  php-mysql php-gd php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring  php-soap curl php-curl
```

- 卸载php

```php
# 删除php的相关包及配置

sudo apt-get autoremove php7*

# 删除关联
    
sudo find /etc -name "*php*" |xargs  rm -rf  
    
# 清除dept列表
 sudo apt purge `dpkg -l | grep php| awk '{print $2}' |tr "\n" " "`
# 检查是否卸载干净（无返回就是卸载完成）
  dpkg -l | grep php7.0
```

# nginx 安装

**因为deepin自带的apache2**所以安装nginx可能会错

> nginx-full nginx E: Sub-process /usr/bin/dpkg returned an error code (1)

```shell
service apache2 status
service apache2 stop
sudo apt autoremove apache2
```



```shell
sudo apt -y  install php-fpm
sudo apt -y  install nginx
```

**配置文件信息**

```shell
server {
    listen 80;
    server_name www.cs.com;
    root /home/yaoliuyang/Documents/work_study/laravel_study/public/;

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
        fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}

```

