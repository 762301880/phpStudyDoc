# 说明

> 本来之前使用的是Ubuntu系统结果无缘无故的崩了(已经是第二次了，虽然这次只是开个机)，
>
> deepin自带的源没有mysql-server，还好网络上有很多解决方案

## 资料

| 名称                                       | 地址                                                         |
| ------------------------------------------ | ------------------------------------------------------------ |
| 第三方博客-Deepin V20 安装MySQL 8 菜鸟教程 | [link](https://bbs.deepin.org/post/191965#mod=viewthread&tid=191965&extra=) |
| 知乎deepin系统使用体验                     | [link](https://zhuanlan.zhihu.com/p/66428353)                |



# 安装

##  [官网下载mysql存储库安装](https://bbs.deepin.org/post/191965#mod=viewthread&tid=191965&extra=)

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

