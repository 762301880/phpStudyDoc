^ 20230227

**参考文档(非搬运)**

> https://blog.csdn.net/qq_44758926/article/details/115280162
>
> https://blog.51cto.com/u_14518853/4894010

# 初识Mysql

>  前端(页面:展示,数据!)
>
> 后台(连接点:连接数据库JDBC,连接前端(控制,控制视图跳转,和给前端传递数据))
>
> 数据库(存数据,Txt,Excel,word)

##  为什么学习数据库

1. 岗位需求
2. 现在的世界,大数据时代~,得数据者得天下
3. 被迫需求:存数据
4. <font color='red'>数据库是所有软件体系中最核心的存在</font> DBA

## 什么是数据库

数据库(DB,DataBase)

概念:数据仓库,软件,安装在操作系统(windows,linux,mac...)之上! SQL,可以存储大量的数据,500万!

作用: 存储数据,管理数据

## 数据库分类

关系型数据库:(SQL)

- Mysql,Oracle,Sql Server,DB2,SQLlite
- 通过表和表之间,行和列之间的关系进行数据的存储,学员信息表, 考勤表,....



非关系型数据库:(NoSql) Not Only

- Redis,MongDB
- 非关系型数据库,对象存储,通过对象的自身的属性来决定

<font color='red'>DBMS(数据库管理系统)</font> 

- 数据库的管理软件,科学有效的管理我们的数据。维护和获取数据;
- MySQL,数据库管理系统!

##  [mysql简介](https://baike.baidu.com/item/MySQL/471251?fr=aladdin)

MySQL是一个**[关系型数据库管理系统](https://baike.baidu.com/item/关系型数据库管理系统/696511?fromModule=lemma_inlink)**

**历史 :** 由瑞典MySQL AB 公司开发，目前属于 Oracle 旗下产品。

MySQL是最好的 [RDBMS](https://baike.baidu.com/item/RDBMS/1048260?fromModule=lemma_inlink) (Relational Database Management System，关系数据库管理系统) 应用软件之一。

开源的数据库软件~  

体积小、速度快、总体拥有成本低

官网[www.mysql.com](https://www.mysql.com/)

mysql官网下载地址 [https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)

## 安装mysql

1、下载后得到zip压缩包.

2、解压到自己想要安装到的目录，本人解压到的是D:\Environment\mysql-5.7.19

3、添加环境变量：我的电脑->属性->高级->环境变量   选择PATH,在其后面添加: 你的mysql 安装文件下面的bin文件夹

4、编辑 my.ini 文件 ,注意替换路径位置

```ini
[mysqld]
# 目录一定要换成自己的
basedir=D:\Program Files\mysql-5.7\
# data\不需要手动创建
datadir=D:\Program Files\mysql-5.7\data\    
port=3306
# 默认不需要登录密码
skip-grant-tables     
```

5、启动管理员模式下的CMD，并将路径切换至mysql下的bin目录，然后输入`mysqld –install `(安装mysql)

6、再输入 `mysqld --initialize-insecure --user=mysql` 初始化数据文件

7、然后再次启动`mysql` 然后用命令 `mysql –u root –p` 进入`mysql`管理界面（密码可为空）

> 进入mysql通过命令行(-p后面不要加空格:因为空格也算字符),修改密码(sql语句后面一定要加;(分号))

8、进入界面后更改root密码

```mysql
update mysql.user set authentication_string=password('123456') where user='root' and Host = 'localhost';
```

9、刷新权限

```mysql
flush privileges;
```

10、修改 my.ini文件删除最后一句(可以使用# 注释)`skip-grant-tables`

11、重启mysql即可正常使用

```mysql
net stop mysql
net start mysql
```



## 安装sqlya

## 连接数据库

```sql
yaoliuyang@yaoliuyang-PC:~$ mysql -u root -p -- 连接
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 13
Server version: 8.0.31 MySQL Community Server - GPL

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

# 修改用户密码
mysql>  update mysql.`user` set authentication_string=PASSWORD('123456') where user='root' and host='localhost';
# 刷新权限使其生效
mysql> FLUSH PRIVILEGES;
#---------------------------------------------------------------------------
#-- 所有的语句都使用;结尾

# 查看所有的数据库
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| housekeeping       |
| information_schema |
| mysql              |
| performance_schema |
| school             |
| sys                |
+--------------------+
6 rows in set (0.00 sec)

mysql> use school  -- 切换 数据库 use 数据库名
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed

mysql> show tables; -- 查看数据库中所有的表
+------------------+
| Tables_in_school |
+------------------+
| student          |
+------------------+
1 row in set (0.00 sec)

mysql> describe student; -- 显示数据库中所有的表的信息
+-------+--------------+------+-----+---------+----------------+
| Field | Type         | Null | Key | Default | Extra          |
+-------+--------------+------+-----+---------+----------------+
| id    | int          | NO   | PRI | NULL    | auto_increment |
| name  | varchar(100) | NO   |     | NULL    |                |
| age   | int          | NO   |     | NULL    |                |
+-------+--------------+------+-----+---------+----------------+
3 rows in set (0.01 sec)


mysql> create database test; -- 创建一个数据库test
Query OK, 1 row affected (2.53 sec)

mysql> show databases; # 再次显示所有的数据库(可以发现多了一个test数据库)
+--------------------+
| Database           |
+--------------------+
| housekeeping       |
| information_schema |
| mysql              |
| performance_schema |
| school             |
| sys                |
| test               |
+--------------------+
7 rows in set (0.00 sec)

mysql> use test
Database changed
mysql> show tables;
Empty set (0.00 sec)


exit; -- 退出数据库

-- 单行注释(SQL 本来的注释)

/*
 多行注释
 hello 
*/
```

**数据库xxx语言 **

DDL 定义

DML 操作

DQL 查询

DCL 控制

# 操作数据库

操作数据库> 操作数据库中的表> 操作数据库中表的数据

<font color='red'>mysql 关键字不区分大小写</font>

## 操作数据库

**创建数据库**

```sql
CREATE DATABASE IF NOT EXISTS school
```

**删除数据库**

```shell
DROP DATABASE IF EXISTS test
```

**使用数据库**

```sql
use `school`    --加单引号会变成字段 特殊的字符一眼加这个符号
# 例如 user 字段是系统专属的字段 会显示高亮这时候加``就可以加以区分
select `user` FROM student
```

**查看数据库**

```sql
show DATABASES;  -- 查看所有的数据库
```

##  [数据库的数据类型](https://www.runoob.com/mysql/mysql-data-types.html)

### 数值

```sql
# 从小到大排列
tinyint               # 十分小的数据   1个字节
SMALLINT              # 较小的数据     2个字节	
MEDIUMINT             # 中等大小的数据  3 个字节
INT 或 INTEGER        # 标准的整数      4个字节   常用的 
BIGINT                # 较大的数据      8 个字节
FLOAT                 # 浮点数(单精度)       4个字节
DOUBLE                # 浮点数(双精度)       8个字节
DECIMAL               # 字符串形式的浮点数(金融计算的时候,一般使用decimal)
```

### 字符串

```sql
CHAR  字符串固定大小的	0-255 bytes	定长字符串
VARCHAR	0-65535 bytes	变长字符串                      # 常用的
TINYBLOB	0-255 bytes	不超过 255 个字符的二进制字符串
TINYTEXT	0-255 bytes	短文本字符串
BLOB	0-65 535 bytes	二进制形式的长文本数据
TEXT	0-65 535 bytes	长文本数据         # 保存大文本
MEDIUMBLOB	0-16 777 215 bytes	二进制形式的中等长度文本数据
MEDIUMTEXT	0-16 777 215 bytes	中等长度文本数据
LONGBLOB	0-4 294 967 295 bytes	二进制形式的极大文本数据
LONGTEXT	0-4 294 967 295 bytes	极大文本数据
```



### 时间

```sql
DATE	3	1000-01-01/9999-12-31	YYYY-MM-DD	日期值 # 年月日
TIME	3	'-838:59:59'/'838:59:59'	HH:MM:SS	时间值或持续时间    # 时分秒 
YEAR	1	1901/2155	YYYY	年份值    # 年
DATETIME	8	'1000-01-01 00:00:00' 到 '9999-12-31 23:59:59'	YYYY-MM-DD hh:mm:ss	混合日期和时间值     # 年月日时分秒  最常用
TIMESTAMP	4	     #  时间戳  1970.1.1 到现在的毫秒数
'1970-01-01 00:00:01' UTC 到 '2038-01-19 03:14:07' UTC

结束时间是第 2147483647 秒，北京时间 2038-1-19 11:14:07，格林尼治时间 2038年1月19日 凌晨 03:14:07

YYYY-MM-DD hh:mm:ss	混合日期和时间值，时间戳
```



### null

```sql
没有值
# 注意 不要使用NULL进行运算,结果为NULL
```



			##  数据库的字段属性

### Unsigned:

> 无符号的整数
>
> 申明了该列不能申明为负数

### **zerofill**

> 0填充的
> 不足的位数，使用0来填充， int（3）， 5 — 005

### **自增：**

> 通常理解为自增，自动在上一条记录的基础上+1（默认）
> 通常用来设计唯一的主见 ~ index，必须是整数类型
> 可以自定义设计主键自增的起始值和步长

###    **非空null not null**

> 假设设置为not null，如果不给它赋值，就会报错！
> null 如果不填写值，默认就是null！

### **默认：**

> 设置默认的值
> sex，默认值为男，如果不指定该列的值，则会有默认的值！

 **创建数据库表（重点）**                   

```sql
/* 每个表，都需要存在以下五个字段  未来做项目用的，表示一个记录存在的意义  拓展
id 主键
'version'  乐观锁
is_delete 伪删除   认为被删除  实际没有
gmt_create 创建时间
gmt_update 修改时间
*/
```



## 创建数据库表

```sql
/*
    目标: 创建一个school数据库
    创建一个学生表(列,字段)  使用sql 创建
    学号 int 登录密码 varchar(20) 姓名,性别varchar(2),出生日期(datetime),家庭住址,email
    注意点,使用英文(),表的名称和字段尽量使用`` 括起来
*/

-- AUTO_INCREMENT 自增
-- PRIMARY KEY 主键
-- 字符串使用单引号括起来!
-- 所有的语句后面加,(英文的),最后一个不用加
-- ENGINE 引擎
-- CHARSET编码

CREATE TABLE IF NOT EXISTS `student`(
	`id` INT(4) NOT NULL AUTO_INCREMENT COMMENT '学号',
	`name` VARCHAR(30) NOT NULL DEFAULT'匿名' COMMENT'姓名',
	`pwd` VARCHAR(20) NOT NULL DEFAULT'123456' COMMENT'密码',
	`sex` VARCHAR(2) NOT NULL DEFAULT'女' COMMENT'性别',
	`birthday` DATETIME DEFAULT NULL COMMENT'出生日期',
	`address` VARCHAR(100) DEFAULT NULL COMMENT'家庭住址',
	`email` VARCHAR(50) DEFAULT NULL COMMENT'邮箱',
	PRIMARY KEY (`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8mb4
```

格式

```sql
create table [if not exists] `表名`(
    `字段名` 列类型 [属性] [索引] [注释],
    `字段名` 列类型 [属性] [索引] [注释],
    ......
    `字段名` 列类型 [属性] [索引] [注释]
)[表类型][字符设置][注释]
```

























































