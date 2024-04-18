# 参考资料

| name                    | url                                                          |
| ----------------------- | ------------------------------------------------------------ |
| 菜鸟教程-MySQL 导出数据 | [链接](https://www.runoob.com/mysql/mysql-database-export.html) |
|                         |                                                              |

# 命令

## 常用命令

```php
-u, --user：指定MySQL登录用户名；
-p, --password：指定MySQL登录密码；
-h, --host：指定MySQL主机名；
-P, --port：指定MySQL端口号；
--socket：指定MySQL socket文件路径；
--protocol：指定MySQL连接协议；
-c, --complete-insert：导出完整的INSERT语句；
-e, --extended-insert：导出扩展的INSERT语句；
--hex-blob：导出二进制数据的十六进制格式；
-t, --no-create-info：只导出表数据，不导出表结构；
-d, --no-data：只导出表结构，不导出表数据；
-R, --routines：导出存储过程和函数；
--triggers：导出触发器；
--events：导出事件；
--single-transaction：在一个事务内导出数据；
--lock-tables：导出数据时锁定表；
--default-character-set：指定导出文件的字符集；
--set-gtid-purged：指定导出文件中要包含哪些GTID信息；
--column-statistics：导出列的统计信息；
--skip-comments：不导出注释信息；
--skip-lock-tables：不锁定表进行导出；
--skip-add-locks：不加锁进行导出；
--skip-disable-keys：不禁用键进行导出；
--skip-triggers：不导出触发器；
--skip-routines：不导出存储过程和函数；
--skip-events：不导出事件；
--skip-opt：不使用优化选项进行导出；
--compact：使用更紧凑的语法进行导出；
--skip-extended-insert：不使用扩展的INSERT语法进行导出；
--skip-comments：不导出注释信息；
--skip-quote-names：不为表和列名加引号；
--where：指定导出的数据条件；
--tables：指定导出的表；
--databases：指定导出的数据库；
--ignore-table：指定要忽略的表；
--no-create-db：不导出CREATE DATABASE语句；
--add-drop-database：在导出文件中添加DROP DATABASE语句；
--add-drop-table：在导出文件中添加DROP TABLE语句；
--add-drop-trigger：在导出文件中添加DROP TRIGGER语句；
--add-locks：在导出文件中添加LOCK TABLE语句；
--add-locks-for-views：在导出文件中添加LOCK TABLE语句，包括视图；
--add-drop-view：在导出文件中添加DROP VIEW语句；
--add-drop-event：在导出文件中
```

## 导出数据库

```sql
# 进入mysql bin 目录中执行mysqldump命令          
# -u root -p laravel_study  -u 后面跟用户名 -p后面跟需要导出的数据库名
# d:/laravel_study.sql 指定路径&sql名称
C:\Program Files\MySQL\MySQL Server 5.7\bin>mysqldump -u root -p laravel_study > d:/laravel_study.sql
Enter password: ******
```

## 导出数据表

```mysql
# -p 数据库 数据库下的数据表
C:\Program Files\MySQL\MySQL Server 5.7\bin>mysqldump -u root -p laravel_study class_s> d:/class_s.sql
Enter password: ******
```

## 导入数据库

```shell
source `+你的sql表地址`
#例子
C:\Program Files\MySQL\MySQL Server 5.7\bin>mysql -uroot -p
Enter password: ******
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 19
Server version: 5.7.25-log MySQL Community Server (GPL)

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| admin              |
| love_wall          |
| monitor            |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
7 rows in set (0.00 sec)
mysql> create database laravel_study; # 创建空数据库
mysql> use laravel_study; # 使用数据库
mysql> source d:/laravel_study.sql # 导入sql
```

#  小案例

## 备份数据库

**参考资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://www.shuzhiduo.com/A/Ae5RR3X25Q/)  [link](https://www.jb51.net/article/222493.htm) |

**编写定时任务脚本**

> 需要保存的目录需要自己保存并记录

```php
# 编写定时任务
mysqldump -u数据库用户名 -p数据库密码 要导出的数据库 > /data/mysql/要导出的数据库_$(date +%Y%m%d_%H%M%S).sql >> /dev/null 2>&1
# 例子  备份今天的数据库并删除三天前的数据库备份
/**
 * 错误写法(/dev/null 2>&1 导致无法成功导致导出成功)
 *  mysqldump -uroot -p123456 mysql > /data/mysql/mysql_$(date +%Y%m%d_%H%M%S).sql >> /dev/null 2>&1 && cd /data/mysql/ && rm -rf    
 *  mysql_$(date -d "3 day ago"  +%Y%m%d).sql
 */
 
 mysqldump -uroot -p123456 mysql /dev/null 2>&1 > /data/mysql/mysql_$(date +%Y%m%d_%H%M%S).sql  && cd /data/mysql/ && rm -rf    
   mysql_$(date -d "3 day ago"  +%Y%m%d).sql
```

mysqldump导出数据库如何不输出错误信息



## **bug复现**

### 错误使用/dev/null 2>&1导致导出数据失败

> 您可以使用以下命令来导出 MySQL 数据库并将错误信息输出到/dev/null中：

```sql
mysqldump -u [用户名] -p [密码] [数据库名] 2>/dev/null > [导出文件名].sql
```

> 这将把标准错误输出重定向到/dev/null（即不输出任何错误信息），并将数据库导出到指定的文件中。

> 请注意，如果在导出过程中发生了任何错误，您将不会看到它们的详细信息。如果您需要查看任何错误信息，您可以省略重定向到/dev/null，然后将输出保存到文件中，以便稍后查看。

```sql
mysqldump -u [用户名] -p [密码] [数据库名] > [导出文件名].sql 2> [错误日志文件名].txt
```

> 这将把标准输出和标准错误输出分别重定向到不同的文件中。您可以将错误日志文件保存到任何您希望的位置，并查看其中的任何错误信息。



## 更多备份参考

备份 MySQL 数据库的方案通常有以下几种：

1. 使用 mysqldump 命令备份：使用这个命令可以备份整个 MySQL 数据库、多个数据库或单个表。例如：

```shell
mysqldump -h localhost -u root -p database_name > /path/to/backup_file.sql
```

> 其中，`-h` 参数指定 MySQL 服务器的地址，`-u` 指定用户名，`-p` 指定密码，`database_name` 指定要备份的数据库名称，`>` 后面的路径是备份文件的路径和名称。

2. 使用 MySQL Enterprise Backup：这是 MySQL 官方提供的备份工具，支持全量备份、增量备份和压缩备份等多种备份方式，备份速度较快，安全性好。

3. 使用第三方工具：比如 Percona XtraBackup、MyDumper 等，这些工具在备份 MySQL 数据库时，支持多种备份方式，支持增量备份和并发备份等高级功能，使用起来相对比较方便。

备份数据库时，需要注意备份的频率和存储的位置，以保证数据库的安全和可靠性。