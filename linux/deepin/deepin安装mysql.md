# 说明



# 安装

## mariadb-替代mysql使用

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

