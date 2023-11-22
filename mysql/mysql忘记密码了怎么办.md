

如果你忘记了 MySQL 数据库的密码，你可以尝试以下几种方法来重置密码：

##  1.**使用 `mysql_secure_installation` 工具**(不推荐)：

> 这是一个 MySQL 提供的官方工具，用于在初始安装时对 MySQL 进行一些基本的安全设置，其中包括重置 root 用户的密码。

```shell
sudo mysql_secure_installation
```

##  2.**使用 MySQL 的安全模式**：

> 这种方法需要你停止 MySQL 服务器，然后重新启动，并且在启动时使用 `--skip-grant-tables` 参数。

```shell
sudo service mysql stop  # 停止 MySQL 服务：
sudo mysqld_safe --skip-grant-tables &           # 以跳过权限检查的方式启动 MySQL：
```

**然后通过以下方式进入 MySQL：**

> 重新打开一个终端

```mysql
mysql -u root
```

**进入 MySQL 后，使用以下命令来更改密码：**

```mysql
# mysql 8
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'you_new_password';
```

**刷新权限**

```sql
FLUSH PRIVILEGES;
```

**退出 MySQL**：

```
sqlCopy code
exit;
```

**重新启动 MySQL**：

```shell
sudo systemctl  start mysql
```