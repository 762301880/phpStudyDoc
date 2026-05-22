## binlog是什么

MySQL 的 `binlog` 全称是：

> **Binary Log（二进制日志）**

正式名称一般叫：

> **MySQL Binary Log**

### binlog 的作用

binlog 是 MySQL 最核心的日志之一，用来记录：

- 所有会修改数据的 SQL
- 或者数据变更事件

例如：

```php
INSERT
UPDATE
DELETE
CREATE TABLE
ALTER TABLE
```



### 它主要用于：

#### 1. 主从复制（Replication）

这是 binlog 最重要的用途。

#### 工作流程

主库：

```
用户执行 SQL
→ 写入数据
→ 同时写 binlog
```

从库：

```
读取主库 binlog
→ 重放 SQL / Event
→ 数据同步
```

所以：

> MySQL 主从同步本质上就是 “同步 binlog”。

举例

主库执行：

```
UPDATE users SET age=18 WHERE id=1;
```

binlog 会记录这次修改。

从库拿到后再执行一次。

这样主从数据一致。

#### 2. 数据恢复（Point In Time Recovery）

可以把数据库恢复到某一个时间点。

这个能力非常重要。

举例

你：

```
DELETE FROM users;
```

结果全表删了。

但你有：

但你有：

- 昨晚全量备份
- 今天的 binlog

那么可以：

最终恢复数据。

#### 3. 审计 / 数据追踪

binlog 可以看到：

- 谁改了数据
- 改了什么
- 什么时间改的

很多公司会：

- 解析 binlog
- 同步到 Kafka
- 做数据分析
- 做 CDC（Change Data Capture）

#### 4. 数据同步 / CDC

很多中间件都依赖 binlog：

例如：

- Canal
- Debezium
- Maxwell

它们监听 binlog 后：

```
MySQL → Kafka → Elasticsearch → ClickHouse
```

实现实时数据同步。





## linux开启binlog

> ```mysql
> #start_binlog
> log_bin=mysql-bin
> binlog_format=ROW
> server_id=1
> ```
>
> 
>
> 新增的 参数写在**mysqld内** 不要写到了**client**

```mysql
[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M
skip-host-cache
skip-name-resolve
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
user=mysql

#start_binlog
log_bin=mysql-bin
binlog_format=ROW
server_id=1


# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

#log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
[client]
socket=/var/run/mysqld/mysqld.sock

!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mysql.conf.d/

```

## binlog 文件长什么样

```php
mysql-bin.000001
mysql-bin.000002
```



## navicat终端查看是否开启**binlog**

> 显示 **ON** 就成功了。
>
> linux日志自动存在 `/var/lib/mysql/`
>
> windows保存在`mysql-8.2.0-winx64\data`

```mysql
# 查看是否开启
show variables like 'log_bin';

# 查看当前 binlog
SHOW MASTER STATUS;

# 查看所有 binlog
SHOW BINARY LOGS;
```

## mysql终端查看

```mysql
bash-4.2# mysql -uroot -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.44-log MySQL Community Server (GPL)

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show variables like 'log_bin_basename';
+------------------+--------------------------+
| Variable_name    | Value                    |
+------------------+--------------------------+
| log_bin_basename | /var/lib/mysql/mysql-bin |
+------------------+--------------------------+
1 row in set (0.01 sec)
```

## 解析binlog

### 导出指定时间删除的sql

> 导出到本地 **这里导出的是解析二进制的信息**  **cmd执行**

```mysql
mysqlbinlog --no-defaults -vv --start-datetime="2026-05-22 17:00:00" --stop-datetime="2026-05-22 18:55:00" .\binlog.000082 > recovery.sql
```