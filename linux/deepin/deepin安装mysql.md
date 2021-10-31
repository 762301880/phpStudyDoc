# 说明



# 安装



```shell
# 在Deepin系统中如何安装MySQL数据库呢？希望这篇文章可以帮到你
#这里。我以安装MySQL的一个分支版本，MariaDB为例，首先打开终端，输入

sudo apt-getinstall mariadb-server mariadb-client

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

#这整个安装过程就完毕了！
```

