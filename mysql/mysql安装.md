#  docker  安装mysql

## 资料

| 名称                     | 地址                                                         |
| ------------------------ | ------------------------------------------------------------ |
| 菜鸟教程-docker安装mysql | [link](https://www.runoob.com/docker/docker-install-mysql.html) |
|                          |                                                              |

[docker-mysql官网](https://registry.hub.docker.com/_/mysql)

```shell
# 下载 默认下载得是最新版 8.*的mysql
docker pull mysql
# 启动 -e(设置环境变量) MYSQL_ROOT_PASSWORD=指定密码  不推荐使用-v h
docker run --name mysql -itd -p 3307:3306 -v /data/mysql:/etc/mysql -e MYSQL_ROOT_PASSWORD=yaoliuyang  容器id
# 执行上一步直接远程连接即可 用户名是root
```

# bug解析

## mysql8以上遇到`The server requested authentication method unknown to the client`

**亲测不行日后再想办法解决**

[参考](https://blog.csdn.net/maoxinwen1/article/details/88629313)

> 修改**mysql.cnf** 配置默认身份验证插件
>
> 找到容器内部`/etc/mysql/conf.d`下的**mysql.cnf**配置使用`vim`打开

```mysql
mysql> use mysql;  # 选择mysql数据库
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> SELECT Host, User, plugin from user;
+-----------+------------------+-----------------------+
| Host      | User             | plugin                |
+-----------+------------------+-----------------------+
| %         | root             | caching_sha2_password |
| localhost | mysql.infoschema | caching_sha2_password |
| localhost | mysql.session    | caching_sha2_password |
| localhost | mysql.sys        | caching_sha2_password |
| localhost | root             | caching_sha2_password |
+-----------+------------------+-----------------------+
5 rows in set (0.00 sec)

#  ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '你的密码';
mysql> ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'yaoliuyang';
Query OK, 0 rows affected (0.01 sec)

# 刷新使其生效  命令本质上的作用是将当前user和privilige表中的用户信息/权限设置从mysql库(MySQL数据库的内置库)中提取到内存里
mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.02 sec)

```

**docker创建mysql容器时挂载文件路径后无法启动已解决**

```php
# 在docker run中加入 --privileged=true  给容器加上特定权限

docker run --name mysql -itd --privileged=true -p 3306:3306 -p 9702:9702 -v /data/mysql:/etc/mysql 
-e MYSQL_ROOT_PASSWORD=yaoliuyang  容器id
