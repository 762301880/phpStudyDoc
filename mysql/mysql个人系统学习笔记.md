^ 20230227

**参考文档(非搬运)**

> https://blog.csdn.net/qq_44758926/article/details/115280162
>
> https://blog.51cto.com/u_14518853/4894010
>
> https://blog.51cto.com/u_15127628/4193070?articleABtest=1
>
> [mysql官方文档](https://dev.mysql.com/doc/refman/5.7/en/)

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

常用命令

```sql
show create database school  -- 查看创造数据库的语句
show create table student -- 查看student数据表定义的语句
desc student -- 显示表的结构
```

## 数据表的类型

```sql
-- 关于数据库引擎
/*
INNODB 默认使用
MYISAM 早些年是用的
*/
```



|            | MYISAM | INNODB         |
| ---------- | ------ | -------------- |
| 事务支持   | 不支持 | 支持           |
| 数据行锁定 | 不支持 | 支持           |
| 外键约束   | 不支持 | 支持           |
| 全文索引   | 支持   | 不支持         |
| 表空间大小 | 较小   | 较大，前者两倍 |



常规使用操作：

> **MYISAM** 节约空间，速度较快
>
> **INNODB** 安全性高，事务的处理，多表多用户操作



**在物理空间存在的位置**

> 所有的数据库文件都存在**data**(压缩包安装后自动生成的那个目录)目录下,一个文件夹就对应一个数据库
>
> 本质还是文件的存储!

MySql引擎在物理文件上的区别

> INNODB 在数据库表中只有一个 `*.frm`文件,以及上级目录下的`ibdata1`文件
>
> MYISAM 对应文件
>
> - `*.frm`  表结构的定义文件
> - `*.MYD`  数据文件(data)
> - `*.MYI`  索引文件(index)

**设置数据库表的字符集编码**

> 不设置的话,会是mysql默认的字符集编码~(不支持中文!)
>
> MySQL的默认编码是Latin1,不支持中文
>
> 在my.ini中配置默认的编码

```sql
CHARSET=utf8mb4
```



## 修改&删除表

### 修改

```sql
-- 修改表名 ALTER TABLE 旧表名 RENAME AS 新表名
ALTER TABLE teacher RENAME AS teacher1
-- 增加表的字段 ALTER TABLE 表名 ADD 字段名 列属性
ALTER TABLE teacher1 ADD age INT(11)
-- 修改表的字段（重命名，修改约束）
-- ALTER TABLE 表名 MODIFY 字段名 列属性[]
ALTER TABLE teacher1 MODIFY age VARCHAR(11) -- 修改约束
-- ALTER TABLE 表名 CHANGE 旧名字 新名字 列属性[]
ALTER TABLE teacher1 CHANGE age age1 INT(11) -- 字段重命名，

-- 删除表的字段 表名 ALTER TABLE 表名 DROP 字段名
ALTER TABLE teacher1 DROP age1
```

### 删除

> <font color='red'>所有的创建和删除操作尽量加上判断，以免报错~</font>

```sql
-- 删除表 DROP TABLE 表名(如果表存在再删除)
DROP TABLE [if exists] teacher1
```

**注意点**

> - `` 字段名,使用反单引号包裹
> - 注释  -- /**/
> - sql 关键字大小不敏感,建议大家写小写
> - 所有的符号全部用英文

# MySQL数据管理

## 外键(了解即可)

> 方式一、在创建表的时候，增加约束（麻烦，比较复杂）

```sql
CREATE TABLE `grade`(
  `gradeid` INT(10) NOT NULL AUTO_INCREMENT COMMENT '年级id',
  `gradename` VARCHAR(50) NOT NULL COMMENT '年级名称',
  PRIMARY KEY(`gradeid`)
)ENGINE=INNODB DEFAULT CHARSET=utf8mb3

-- 学生表的gradeid字段 要去引用年纪表的gradeid
-- 定义外键key
-- 给这个外键添加约束(执行引用)   REFERENCES 引用
CREATE TABLE IF NOT EXISTS `student`(
	`id` INT(4) NOT NULL AUTO_INCREMENT COMMENT '学号',
	`name` VARCHAR(30) NOT NULL DEFAULT'匿名' COMMENT'姓名',
	`pwd` VARCHAR(20) NOT NULL DEFAULT'123456' COMMENT'密码',
	`sex` VARCHAR(2) NOT NULL DEFAULT'女' COMMENT'性别',
	`birthday` DATETIME DEFAULT NULL COMMENT'出生日期',
	`gradeid` INT(10) NOT NULL COMMENT '年级id',
	`address` VARCHAR(100) DEFAULT NULL COMMENT'家庭住址',
	`email` VARCHAR(50) DEFAULT NULL COMMENT'邮箱',
	PRIMARY KEY (`id`),
	KEY `FK_gradeid` (`gradeid`), -- FK_ 外键约束规定
	CONSTRAINT `FK_gradeid` FOREIGN KEY (`gradeid`) REFERENCES `grade`(`gradeid`)
)ENGINE=INNODB DEFAULT CHARSET=utf8mb4

```

删除有外键关系的表的时候，必须要先删除引用别人的表，再删除被引用的表

```sql
# 如果新建表的时候没有创建外间可以使用alter 新增一个外键约束

ALTER TABLE `student`  ADD CONSTRAINT `FK_gradeid` FOREIGN KEY(`gradeid`) REFERENCES `grade`(`gradeid`)

-- ALTER TABLE 表 ADD CONSTRAINT 约束名 FOREIGN KEY (作为外键的列) REFERENCES 哪个表(引用的列)
```

以上的操作都是物理外键，数据库级别的外键，不建议使用！（避免数据库过多困扰）

最佳实践

- 数据库就是单纯的表，只用来存数据，只有行（数据）和列（字段）
- 我们想使用多张表的数据，想使用外键（程序去实现）



##  DML语言(全部记住,背下来)

> 数据库意义: **数据存储,数据管理**
>
> DML 语言: 数据操作语言

- insert 添加
- update 修改
- delete 删除

## 添加

> 注意事项
>
> 1. 字段和字段之间使用英文逗号隔开
> 2. 字段是可以省略的,但是后面的值必须要一一对应,不能少
> 3. 可以同时插入多条数据,VALUES后面的值,需要使用,(逗号)隔开即可`values（),()`

```sql
# 语法：INSERT INTO 表名 ([列1],[列2],[列3]) VALUES ('字段1','字段2','字段3'),('字段1','字段2','字段3')

INSERT INTO `grade` (`gradename`) VALUES ('大四')

-- 由于主见自增我们可以省略(会报错)   如果不写表的字段,他就会一一匹配
INSERT INTO `grade`  VALUES ('大三')

-- 一般写入插入语句,我们一定要数据和字段一一对应!

-- 插入多个字段
insert into `grade` (`gradename`) values('大二'）,（'大一')

INSERT INTO `student` (`name`,`pwd`,`sex`) VALUES ('张三','aaa','男'),('王五','bbb','男')
```



## 修改

> update 修改谁  (条件)  set 原来的值=新值

```sql
UPDATE `student` SET `name` = '帅哥',`pwd` = '789' WHERE id =8

-- 不指定条件的情况下,会改动所有表中的记录
UPDATE `student` SET `name` = 'all帅哥',`pwd` = '789' 

-- 通过多个条件定位数据,无上线

update `student` set `name`='新名称' where `name`='all帅哥' and sex='男'

--  语法：
-- update 表名 set 列=值,[列=值],[列=值] where [条件]
```

> 条件:where 子句 运算符 id 等于某个值,大于某个值,在某个区间内修改...

操作值会返回 布尔值

| 操作符       | 含义       | 范围        | 结果  |
| ------------ | ---------- | ----------- | ----- |
| =            | 等于       | 5=6         | false |
| <>或!=       | 不等于     | 5<>6        | true  |
| >            |            |             |       |
| <            |            |             |       |
| <=           |            |             |       |
| >=           |            |             |       |
| between…and… | []闭合区间 |             |       |
| and          | 相当于&&   | 5>1 and 1>2 | false |
| or           | 相当于\|\| | 5>1 or 1>2  | true  |

注意:

- clonum_name 是数据库的列,尽量带上``
- 条件,筛选的条件,如果没有指定,则会修改所有的列
- value,是一个具体的值,也可以是一个变量

```sql
update `student` set `birthday`=CURRENT_TIME WHERE `name`='all帅哥' and sex='女'
```

- 多个设置的属性之间,使用英文逗号隔开



## 删除

**delete**命令

> 语法`delete from 表名 [where条件]`

```sql
-- 删除数据(避免这样写,没有条件会直接删除全部表中的数据) 

DELETE FROM `student` 

-- 删除指定的数据
DELETE FROM `student` where id=8
```

**truncate**命令

作用:完全清空一个数据库表,表的结构和索引约束不会变!

```sql
TRUNCATE   student
```

**delete和truncate区别**

- 相同点:都能删除数据,都不会删除表结构
- 不同
  - truncate 重新设置自增列计数器会归零
  - truncate  不会影响事务

了解即可：`DELETE`，重启数据库，现象

- innoDB 自增列会从1开始（存在内存中的，断电即失）
- MyISAM 继续从上一个自增量开始。（存在文件中，不会丢失）

# DQL查询数据(最重点)

## DQL

(Data Query LANGUAGE:数据查询语言)

- 所有的查询操作都用它 `Select`
- 简单的查询,复杂的查询它都能做~
- <font color='red'>数据库中最核心的语言,最重要的语句</font>
- 使用频率最高的语句

select 完整的语法

```sql
SELECT [ALL | DISTINCT]
{*l table.* l [table,field1[as alias1][,table.field2[as alias2]][,...]]}
FROM table_name [as table_alias]
[left | right | inner join table_name2] --联合查询
[WHERE ...]--指定结果需满足的条件
[GROUP BY ...]-- 指定结果按照哪几个字段来分组
[HAVING] -- 过滤分组的记录必须满足的次要条件
[ORDER BY ...]--指定查询记录按一个或多个条件排序
[LIMIT {[offset,]row_count l row_countOFFSET offset]];--指定查询的记录从哪条至哪条
```



## 指定查询字段

```sql
-- 查询全部的学生 select 字段 from 表            * 通配符
SELECT * FROM student

-- 查询指定字段
SELECT `studentno`,`studentname` FROM student 

-- 别名，给结果起一个名字 AS
SELECT `studentno` AS 学号,`studentname` AS 学生姓名 FROM student -- 表也可以加AS 给表起一个别名

-- 函数 concat(a,b)
SELECT CONCAT('姓名：',studentname) AS 新名字 FROM student

```

语法: `select 字段,... from 表`

> 有的时候,列名字不是那么的见名知意,我们起别名 AS  字段名 as 别名    表名  as 别名



**去重** distinct

> 作用: 去除 select 查询出来的结果中重复的数据,重复的数据只显示一条

```sql
-- 查询一下有哪些同学参加了考试
SELECT * FROM result  -- 查询全部的考试成绩
SELECT `studentno` FROM result  -- 查询全部的考试成绩
-- 去重复
SELECT DISTINCT `studentno` FROM result 
```

**数据库的列(表达式)**

```sql
SELECT VERSION() -- 查询系统版本(函数)

SELECT 100*3-1 AS 计算结果 -- 用来计算(表达式)

SELECT @@auto_increment_increment  -- 查询自增的步长（变量）
 
-- 学员考试成绩表 + 1分查看 StudentResult==分数
select `StudenNo`,`StudentResult`+1 as `提分后` from result 
```

**数据库中的表达式**: 文本值,列,Null,函数,计算表达式,系统变量...

select 表达式  from 表

## where条件子句

作用:检索数据中**符合条件的值**的值

搜索的条件由一个或者多个表达式组成! 结果 布尔值

**逻辑运算符**

| 运算符  | 语法          | 描述                         |
| ------- | ------------- | ---------------------------- |
| and &&  | a and b a&&b  | 逻辑与，两个都为真，结果为真 |
| or \|\| | a or b a\|\|b | 逻辑或                       |
| not !   | not a !a      | 逻辑非，取反                 |

```sql
-- 查询考试成绩在 95～100分之间
select studentNo,studentREsult from result where studentREsult >=95 and studentREsult<=100


-- 模糊查询(区间)    查询考试成绩在 95～100分之间
select studentNo,studentREsult from result where studentREsult between 95 and 100

-- 除了1000号学生之外的同学的成绩
select studentNo,studentREsult from result where studentREsult !=1000

-- != not
select studentNo,studentREsult from result where not studentREsult =1000
```



模糊查询：比较运算符

| **运算符**  | **语法**          | **描述**                                 |
| ----------- | ----------------- | ---------------------------------------- |
| IS NULL     | a is null         | 如果操作符为null，结果为真               |
| IS NOT NULL | a is not null     | 如果操作符为不为 null，结果为真          |
| BETWEEN     | a between b and c | 若a在b和c之间，结果为真                  |
| **Like**    | a like b          | SQL匹配，如果a匹配b，则结果为真          |
| **in**      | a in(a1,a2,a3…)   | 假设a在a1，a2，a3…其中的某一个，结果为真 |

```sql
SELECT * FROM student where  ISNULL(address)

-- 查询姓刘的同学
-- like 结合 %(代表0到任意个字符)  _(一个字符)
select `studentNo`,`studentName` from student where `studentName` like "刘%"

-- 查询姓刘的同学,名字后面只有一个字的
select `StudentNO`,`StudentName` from student where `StudentName` like "刘_"

-- 查询姓刘的同学,名字后面只有两个字的(两个下划线)
select `StudentNO`,`StudentName` from student where `StudentName` like "刘__"

-- 查询名字后中间有嘉字的同学
select `StudentNO`,`StudentName` from student where `StudentName` like "%嘉%"

-- ======================== in (具体的一个或多个值) ========================
-- 查询 1001,1002,1003号学员
select `StudentNO`,`StudentName` from student where `StudentNO` in (1001,1002,1003)



-- ======================== null not null ========================
-- 查询地址为空的学生

select `StudentNO`,`StudentName` from student where `address`='' or `address` is null
select `StudentNO`,`StudentName` from student where ISNull(`address`)

-- 查询地址不为空的学生
select `StudentNO`,`StudentName` from student where `address` is not NULL
select `StudentNO`,`StudentName` from student where `address` !=''

```

## [联表查询](https://www.runoob.com/mysql/mysql-join.html)

JOIN

![img](https://ask.qcloudimg.com/http-save/yehe-1558882/986tlzgtra.png)



```sql
-- ======================联表查询 join ==============================
-- 查询参加考试的同学 （学号，姓名，考试编号，分数）

SELECT * FROM student 
SELECT * FROM result

/*
1. 分析需求，分析查询的字段来自哪些表
2.确定使用哪种连接查询？7种
确定交叉点（这两个表中哪个数据是相同的）
判断的条件： 学生表中 studentNo = 成绩表中 studentNo 

*/

-- JION（表） ON （判断的条件）连接查询
-- where 等值查询
SELECT s.studentNo,studentName,SubjectNo,StudentResult
FROM student AS s
INNER JOIN result AS r
ON s.studentNo=r.studentNo

--Right Join
SELECT s.studentNo,studentName,SubjectNo,StudentResult
FROM student AS s
RIGHT JOIN result AS r
ON s.studentNo = r.studentNo

--LEFT Join
SELECT s.studentNo,studentName,SubjectNo,StudentResult
FROM student AS s
LEFT JOIN result AS r
ON s.studentNo = r.studentNo
```



| 操作       | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| inner join | 如果表中至少有一个匹配,就返回行  (指的是链接表点的双方有一个只非空,则都会返回记录) |
| left join  | 即使左表中没有匹配,也会从左表中返回所有的值(以左表的非空交叉点为主,右表的是否非空无关) |
| rigth join | 即使右表中没有匹配,也会从右表中返回所有的值(是以右表的交叉点非空判断,与左表无关) |

```sql
-- join on 连接查询  join 连接的表, On的意思是判断的条件
-- where 等值查询

-- 查询考的同学
SELECT s.studentNo,studentName,SubjectNo,StudentResult
FROM student AS s
LEFT JOIN result AS r
ON s.studentNo = r.studentNo
WHERE StudentResult IS NULL

-- 查询了参加考试同学的信息：学号：学生姓名：科目名：分数
SELECT s.`studentNo`,`studentName`,`SubjectName`,`studentResult`
FROM student s
RIGHT JOIN result r
ON r.studentNo=s.studentNo
INNER JOIN `subject` sub
ON r.SubjectNo=sub.SubjectNo

-- 我要查询哪些数据 SELECT ....
-- 从哪几个表中查 FROM 表 xxx JOIN 连接的表 ON 交叉条件
-- 假设存在一中多张表查询，先查询两章表，然后再慢慢增加

-- FROM a LEFT JOIN b   左为准
-- FROM a RIGHT JOIN b	右为准


```

### **自连接**(类似于无限级评论自己连接自己)

> 自己的表和自己的表连接,核心:<font color='yellow'>一张表拆为两张一样的表即可</font>

**创建数据表**

```sql
-- 创建数据表
CREATE TABLE `category` (
  `categoryid` int NOT NULL AUTO_INCREMENT,
  `pid` int DEFAULT NULL COMMENT '父id',
  `categoryName` varchar(20) COLLATE utf8mb4_general_ci DEFAULT NULL,
  PRIMARY KEY (`categoryid`)
) ENGINE=InnoDB AUTO_INCREMENT=9 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci COMMENT='类别表';

# 插入测试数据
INSERT INTO `category` ( `categoryid`, `pid`, `categoryName` )
VALUES
    (3,1,'软件开发' ),(4,3,'数据库' ),
	(5,1,'美术设计'),(6,3,'web开发'),(7,5,'ps技术'),
	(8,2,'办公信息'),(1,1,'信息技术')
	
# 生成的数据库信息如下
+------------+------+--------------+
| categoryid | pid  | categoryName |
+------------+------+--------------+
|          2 |    1 | 信息技术     |
|          3 |    1 | 软件开发     |
|          4 |    3 | 数据库       |
|          5 |    1 | 美术设计     |
|          6 |    3 | web开发      |
|          7 |    5 | ps技术       |
|          8 |    2 | 办公信息     |
+------------+------+--------------+
```

**一级id(父id代表一级)**

| categoryid | categoryName |
| ---------- | ------------ |
| 2          | 信息技术     |
| 3          | 软件开发     |
| 5          | 美术设计     |

子类(父级别不是一级的子集id)

| pid           | categoryid (自己的id) | categoryName |
| ------------- | --------------------- | ------------ |
| 3  (软件开发) | 4                     | 数据库       |
| 2  (信息技术) | 8                     | 办公信息     |
| 3  (软件开发) | 6                     | web开发      |
| 5  (美术设计) | 7                     | ps技术       |



**操作:** 查询父类对应的子类关系

| 父类     | 子类     |
| -------- | -------- |
| 信息技术 | 办公信息 |
| 软件开发 | 数据库   |
| 软件开发 | web开发  |
| 美术设计 | ps技术   |

**对应sql**

```sql
-- 查询父子信息:把一张表看为两个一模一样的表
mysql> SELECT
	a.`categoryName` AS '父名称',
	b.`categoryName` AS '子名称',
	a.`categoryid` AS 'b_ctegoryid',
	b.`categoryid` AS 'b_categoryid',
	a.`pid` AS 'a_pid',
	b.`pid` AS 'b_pid' 
FROM
	`category` AS a
	INNER JOIN `category` AS b ON a.categoryid = b.pid 
WHERE
	a.pid = 1;
+--------------+--------------+-------------+--------------+-------+-------+
| 父名称       | 子名称       | b_ctegoryid | b_categoryid | a_pid | b_pid |
+--------------+--------------+-------------+--------------+-------+-------+
| 软件开发     | 数据库       |           3 |            4 |     1 |     3 |
| 软件开发     | web开发      |           3 |            6 |     1 |     3 |
| 美术设计     | ps技术       |           5 |            7 |     1 |     5 |
| 信息技术     | 办公信息     |           2 |            8 |     1 |     2 |
+--------------+--------------+-------------+--------------+-------+-------+
4 rows in set (0.00 sec)
```

## 分页和排序

### 排序(order by)

> order by  通过那个字段排序怎么排
>
> 排序分为 
>
> - 升序 ASC
> - 降序 DESC

```sql
SELECT s.`StudentNo`,`StudentName`,`SubjectName`,`StudentResult`
FROM student s
INNER JOIN `result` r
ON s.`StudentNo`=r.`StudentNo`
INNER JOIN `subject` sub
ON r.`subjectNo`=sub.`subjectNo`
WHERE subjectName='数据结构-1'
ORDER BY StudentResult ASC
```

### 分页

> 100万条数据
>
> 为什么要分页?
>
> 网页应用:当前,总的页数,页面的大小
>
> 缓解数据库压力,给人的体验更好,瀑布流
>
> 语法: limit 当前页面,页面的大小 **LIMIT 0,5**

```sql
SELECT s.`StudentNo`,`StudentName`,`SubjectName`,`StudentResult`
FROM student s
INNER JOIN `result` r
ON s.`StudentNo`=r.`StudentNo`
INNER JOIN `subject` sub
ON r.`subjectNo`=sub.`subjectNo`
WHERE subjectName='数据结构-1'
ORDER BY StudentResult ASC
LIMIT 0,5

-- 第一页  limit 0,5   (1-1)*5
-- 第二页  limit 5,5   (2-1)*5   
-- 第三页  limit 10,5  (3-1)*5 
-- 第N页   limit N,5   (N-1)*pageSize,pageSize
-- [pageSize:页面大小]
-- [(n-1)*pageSize起始值]
-- [n:当前页]
-- [数据总数/页面大小=总页数]
```

语法:  limit  (查询起始下标,pageSize)

## 子查询

> where (值是固定的,这个值是计算出来的)
>
> 本质: 在where语句中嵌套一个子查询语句
>
> where (select * from)

```sql
-- ===========================where=========================

-- 1.查询 数据库结构-1的所有考试结构（学号，科目编号，成绩） 降序排列
-- 方式一： 连接查询
SELECT `StudentNo`,r.`SubjectName`,`StudentResult`
FROM `result` r
INNER JOIN `subject` sub
ON r.SubjectNo = sun.SubjectNo
WHERE subjectName = '数据库结构-1'
ORDER BY StudentResult DESC

-- 方式二：使用子查询(由里及外 先查询自查询)
SELECT `StudentNo`,r.`SubjectName`,`StudentResult`
FROM `result`
WHERE StudentNo=(
	SELECT SubjectNo FROM  `subject` 
    WHERE SubjectName = '数据库结构-1'
)
ORDER BY StudentResult DESC


-- 分数不少于80分的学生的学号和姓名
SELECT DISTINCT s.`StudentNo`,`StudentName`
FROM student s
INNER JOIN result r
ON r.StudentNo = s.StudentNo
WHERE StudentResult>=80

-- 在这个基础上 增加一个科目 ，高等数学-2
SELECT DISTINCT s.`StudentNo`,`StudentName`
FROM student s
INNER JOIN result r
ON r.StudentNo = s.StudentNo
WHERE StudentResult>=80 AND `SubjectNo`=(
    SELECT Subject FROM `subject`
    WHERE SubjectName='高等数学-2'
)

-- 查询课程为 高等数学-2 且分数不小于80分的同学的学号和姓名
SELECT s.`StudentNo`,`StudentName`
FROM student s
INNER JOIN result r
ON s.StudentNo = r.StudentNo
INNER JOIN `subject` sub
ON r.`SubjectName`='高等数学-2'
WHERE `SubjectaName`='高等数学-2' AND StudentResult >=80


-- 再改造 (由里即外)
SELECT `StudentNo`,`StudentName` FROM student
WHERE StudentNo IN(
SELECT StudentNo result WHERE StudentResult >80 AND SubjectNo =(
SELECT SubjectNo FROM `subject` WHERE `SubjectaName`='高等数学-2'
)
)


```

## 分组和过滤

```sql
-- 查询不同课程的平均分，最高分，最低分，平均分大于80
-- 核心：（根据不同的课程分组）

SELECT `SubjectName`,AVG(StudentResult),MAX(StudentResult)
FROM result r
INNER JOIN `Subject` sub
ON r.SubjectNo=sub.SubjectNo

GROUP BY r.SubjectNo -- 通过什么字段来分组
HAVING AVG(StudentResult)>80
```

# MYSQL函数

## **常用函数**

```sql
-- 数学运算

SELECT ABS(-8) -- 绝对值     8
SELECT CEILING(9.4) -- 向上取整   10
SELECT FLOOR(9.4)  -- 向下取整    9   
SELECT RAND() -- 返回0-1随机数
SELECT SIGN(-10) -- 判断一个数的符号 0-0 负数返回-1 正数返回1

-- 字符串函数
SELECT CHAR_LENGTH('2323232') -- 返回字符串长度
SELECT CONCAT('我','233') -- 拼接字符串
SELECT INSERT('java',1,2,'cccc') -- 从某个位置开始替换某个长度
SELECT UPPER('abc') 
SELECT LOWER('ABC')
SELECT REPLACE('坚持就能成功','坚持','努力')    -- 努力就能成功
SELECT SUBSTR("狂神说坚持就能成功",4,6)       -- 返回指定的字符串      坚持就能成功
SELECT REVERSE('dcba')     -- 反转字符串  abcd



-- 查询姓 周 的同学 ，改成邹
SELECT REPLACE(studentname,'周','邹') FROM student
WHERE studentname LIKE '周%'

-- 时间跟日期函数（记住）
SELECT CURRENT_DATE() -- 获取当前日期      2023-03-13
SELECT CURDATE() -- 获取当前日期          2023-03-13
SELECT NOW() -- 获取当前日期             2023-03-13 22:14:26
SELECT LOCATIME()  -- 本地时间          2023-03-13 22:14:59
SELECT SYSDATE()  -- 系统时间         2023-03-13 22:15:13

SELECT YEAR(NOW())         
SELECT MONTH(NOW())        
SELECT DAY(NOW())          
SELECT HOUR(NOW())       
SELECT MINUTE(NOW())
SELECT SECOND(NOW())

-- 系统
SELECT SYSTEM_USER()
SELECT USER()
SELECT VERSION()

```

## 聚合函数(常用)

| **函数名称** | **描述** |
| ------------ | -------- |
| COUNT()      | 计数     |
| SUM()        | 求和     |
| AVG()        | 平均值   |
| MAX()        | 最大值   |
| MIN()        | 最小值   |

```sql
-- ============聚合函数=============
-- 都能够统计表中的数据 (想查询一个表中有多少个记录,就使用这个count()
select count(studentname) from student; -- count(指定列,会忽略所有的null值
select count(*) from student; -- count(*),不会忽略所有的null值   本质 计算行数
select count(1) from student;  -- count(1),不会忽略所有的null值     本质 计算行数

select sum(`studentResult`) as 总和 from result
select avg(`studentResult`) as 平均分 from result
select max(`studentResult`) as 最高分 from result
select min(`studentResult`) as 最低分 from result

-- 查询不同课程的平均分,最高分,最低分,平均分大于80
-- 核心:根据不同的课程分组

select `subjectname`,avg(`studentResult`) as 平均分 ,max(`studentResult`) as 最高分,min(`studentResult`) as 最低分 from result r 
inner join `subject` sub on r.`subjectNo`= sub.`subjectNo`
group by r.subjectNo  -- 通过什么字段来分组
having (平均分>=80)
```

##  数据库级别的MD5加密(扩展)

> **什么是MD5?**
>
> 主要增强算法复杂度和不可逆性.
>
> MD5不可逆,具体的值的md5是一样的
>
> MD5破解网站的原理,背后有一个字典,`MD5加密后的值`,加密的前的值

```sql
CREATE TABLE `testmd5` (
	`id` INT NOT NULL AUTO_INCREMENT,
	`name` VARCHAR ( 20 ) NOT NULL COMMENT '名称',
	`pwd` VARCHAR ( 50 ) NOT NULL COMMENT '密码',
PRIMARY KEY ( `id` ) 
) ENGINE = INNODB AUTO_INCREMENT = 3 DEFAULT CHARSET = utf8mb3;


-- 明文密码
INSERT INTO `testmd5` ( `name`, `pwd` )
VALUES
	(
	'张三',
	123456)
-- 加密

	UPDATE testmd5 
SET `pwd` = MD5( `pwd` )  
WHERE
	NAME = "张三"
	
-- 插入的时候加密
INSERT INTO `testmd5` ( `name`, `pwd` )
VALUES
	(
	'张三',
	MD5( 123456 ))
	
-- 如何校验，将用户传递过来的密码，进行MD5加密，然后对比加密后的值
SELECT * FROM testmd5 WHERE `name`='红' AND pwd=MD5('123456')
```

# [事务](https://www.runoob.com/mysql/mysql-transaction.html)

> 什么是事务
>
> 要么都成功,要么都失败
>
> 1、sql 执行 A 给 B转账    A 1000  ->200  B 200
>
> 2、sql执行  B 收到A的钱  A 800  -> B 400
>
> 将一组SQL放在一个批次中去执行
>
> **事务原则**: ACID原则,**原子性,一致性,隔离性,持久性** (脏读,幻读....)
>
> [参考博客](https://blog.csdn.net/dengjili/article/details/82468576)
>
> - 原子性(**Atomicity**)
>
>   要么都成功,要么都失败
>
> - 一致性(**Consistency**)
>
>    事务前后的数据完整性要保持一致,1000
>
> - **持久性（Durability）**
>
>    事务一旦提交则不可逆,被持久化到数据库中!
>
> - 隔离性(**Isolation**)
>
>   事务的隔离性是多个用户并发访问数据库时，数据库为每一个用户开启的事务，不能被其他事务的操作数据所干扰，多个并发事务之间要相互隔离
>
>   **隔离所导致的一些问题**
>
> - 脏读:
>
>  指一个事务读取了另外一个事务未提交的数据
>
> - 不可重复读:
>
> 在一个事务内读取表中的某一行数据,多次读取结果不同。(这个不一定是错误,只是某些场合不对)
>
> - 幻读
>
> 是指在一个事务内读取到了别的事务插入的数据,导致前后读取不一致



## 执行事务

```sql
-- mysql 是默认开启事务自动提交的

SET autocommit = 0; /* 关闭 */
SET autocommit = 1; /* 开启(默认的) */

-- 手动处理事务
SET autocommit = 0; -- 先关闭自动条件
-- 事务开始
START TRANSACTION  -- 标记一个事物的开始，从这之后的sql都在一个事物内


-- 提交： 持久化（成功！）
COMMIT

-- 回滚： 回到原来的样子（失败！）
ROLLBACK

--事物结束
SET autocommit = 1; -- 结束后开启自动提交

-- 了解
SAVEPOINT 保存点名  -- 设置一个事物的保存点
ROLLBACK SAVEPOINT -- 回滚到保存点
RELEASE SAVEPOINT -- 撤销保存点


```

**模拟场景**

```sql
-- 创建商店数据库

CREATE DATABASE shop CHARSET  utf8mb4


-- 创建数据表

-- 账户表
CREATE TABLE `account` (
  `id` int NOT NULL AUTO_INCREMENT,
  `name` varchar(20) NOT NULL,
  `money` decimal(9,2) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
-- 插入数据
INSERT INTO `account` (`name`,`money`) values ('A',2000),('B',10000)

-- 示例
mysql> show tables;
+----------------+
| Tables_in_shop |
+----------------+
| account        |
+----------------+
1 row in set (0.00 sec)

mysql> select * from account;
+----+------+----------+
| id | name | money    |
+----+------+----------+
|  1 | A    |  2000.00 |
|  2 | B    | 10000.00 |
+----+------+----------+
2 rows in set (0.00 sec)



-- #-------模拟事务---------
-- SET autocommit = 0; -- 关闭自动提交
	
START TRANSACTION; -- 开启一个事务

UPDATE 	`account` set money=money-500 where `name` = 'A'; -- A 减500
UPDATE 	`account` set money=money+500 where `name` = 'B'; -- B 加500

COMMIT; -- 提交事务
ROLLBACK; -- 回滚

-- SET autocommit = 1;

```

# 索引

> MySQL官方对索引的定义为:**索引(Index)是帮助MySQL高效获取数据的数据结构。**提取句子主干，就可以得到索引的本质:索引是数据结构。

## 索引的分类

- 主键索引 (PRIMARY KEY)

  > 唯一的标识,主键不可重复,只能有一个列作为主键

- 唯一索引 (UNIQUE KEY)

  > 避免重复的列出现，唯一索引可以重复，多个列都可以标识位

- 常规索引 （KEY/INEDEX）

  > 默认的,index key 关键字来设置

- 全文索引 FULLTEXT

> 在特定的数据库引擎下才有,MyISAM
>
> 快速定位数据

```sql
SHOW INDEX FROM student  -- 显示所有的索引信息

-- 增加一个全文索引 （索引名）列名
ALTER TABLE school.`student` ADD FULLTEXT INDEX `wuhu`(`studentname`)

-- EXPLAIN 分析sql执行的状况
EXPLAIN SELECT * FROM student;  -- 非全文索引

EXPLAIN SELECT * FROM student WHERE MATCH
```

**测试索引**

```sql
-- 新增数据表
CREATE TABLE `app_user` (
  `id` int NOT NULL AUTO_INCREMENT,
  `name` varchar(50) COLLATE utf8mb4_general_ci DEFAULT NULL COMMENT '用户昵称',
  `email` varchar(50) COLLATE utf8mb4_general_ci NOT NULL COMMENT '用户邮箱',
  `phone` varchar(20) COLLATE utf8mb4_general_ci DEFAULT NULL COMMENT '手机号',
  `password` varchar(100) COLLATE utf8mb4_general_ci NOT NULL COMMENT '密码',
  `gender` tinyint DEFAULT '0' COMMENT ' 性别 0男 1 女',
  `age` tinyint DEFAULT '0' COMMENT '年龄',
  `create_time` datetime DEFAULT NULL,
  `update_time` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci COMMENT='app用户表';


-- 插入100万条数据  如果报错 This function has none of DETERMINISTIC, NO SQL, or READS SQL DATA in its de            
-- 参考  https://blog.csdn.net/mutouren_abc/article/details/120719349

set global log_bin_trust_function_creators=TRUE;
DELIMITER $$
CREATE FUNCTION mock_data() 
RETURNS INT
BEGIN
     DECLARE num INT DEFAULT 1000000;
		 DECLARE i INT DEFAULT 0;
		 
		 WHILE i<num DO
		   -- 插入语句

INSERT INTO `app_user` (`name`,`email`,`phone`,`gender`,`password`,`age`) VALUES(CONCAT('用户',i),'762301880@qq.com',
CONCAT('18',FLOOR(RAND()*((999999999-100000000)+100000000))),FLOOR(RAND()*2),UUID(),FLOOR(RAND()*100));
       
			 set i= i+1;
     END WHILE;
        RETURN i;
END;
SELECT mock_data();
```



```sql
SELECT * FROM app_user WHERE `name` = '用户99999' -- 1.191 sec
SELECT * FROM app_user WHERE `name` = '用户99999' -- 1.070 sec

EXPLAIN SELECT * FROM app_user WHERE `name` = '用户99999' -- 1.070 sec

EXPLAIN SELECT * FROM app_user

-- id_表名_字段名 规范
-- CREATE INDEX 索引名 on 表（字段）
CREATE INDEX id_app_user_name ON app_user(`name`);
SELECT * FROM app_user WHERE `name` = '用户99999'  -- 0.006 sec
```

**索引在小数据量的时候，用处不大，但是在大数据的时候，区别十分明显~**

## 索引原则

> 索引不是越多越好
>
> 不要对经常变动的数据加索引
>
> 小数据量的表不需要加索引
>
> 索引一般加在常用来查询的字段上!

[**索引的数据结构**](https://blog.codinglabs.org/articles/theory-of-mysql-index.html)

Hash      类型的索引

BTREE  : InnoDB的默认数据结构

# 权限管理和备份



##  用户管理

用户表: **mysql.user**

本质:**对这张表进行增删改差**

```sql
-- 创建用户
CREATE USER yaoliuyang IDENTIFIED BY '123456'

-- 修改密码(修改当前用户密码)
SET PASSWORD = PASSWORD('123456')

-- 修改密码（修改指定用户密码）
SET PASSWORD FOR yaoliuyang = PASSWORD('123456')

-- 重命名  RENAME USER 原用户名 TO 新用户名
RENAME USER yaoliuyang TO yaoliuyang01
  
-- 用户授权  GRANT ALL PRIVILEGES（全部权限）库.表 TO 用户             *.*表示全部的库全部的表
-- ALL PRIVILEGES 除了给别人授权，其他都能干 
GRANT ALL PRIVILEGES ON *.* TO yaoliuyang -- 全部库，全部表

-- 查询权限 SHOW GRANTS FOR 用户
SHOW GRANTS FOR yaoliuyang -- 查看指定用户的权限
SHOW GRANTS FOR root@localhost -- root用户要加@地址

-- 撤销权限 REVOKE 权限，在哪个库，哪个表撤销，给谁撤销
REVOKE ALL PRIVILEGES ON *.* FROM yaoliuyang

-- 删除用户
DROP USER yaoliuyang 

```

# MySQL备份

> 为什么要备份?
>
> 保证重要的数据不丢失
>
> 数据转移 A--->B

MySQL数据库备份方式

- 直接拷贝物理文件
- 在可视化工具上导出
- 使用命令行导出 mysqldump 命令行 cmd才叫命令行

```sql
# mysqldump -h 主机 -u 用户名 -p密码 数据库 表 > 物理磁盘位置/文件名
# mysqldump -h 主机 -u 用户名 -p密码 数据库 表1 表2 表3 > 物理磁盘位置/文件名
# mysqldump -h 主机 -u 用户名 -p密码 数据库> 物理磁盘位置/文件名

C:\Program Files\MySQL\MySQL Server 5.7\bin>mysqldump -hlocalhost -uroot -p123456 laravel_study > d:/laravel_study.sql

# 导入
# 登录的情况下，切换到指定的数据库
# source 备份文件
source d:/a.sql
```

#  规范数据库设计

##  为什么需要设计

> **当数据库比较复杂的时候,我们就需要设计了**
>
> 糟糕的数据库设计
>
> - 数据冗余,浪费空间
> - 数据库插入和删除都会麻烦、异常 [屏蔽使用物理外键]
> - 程序的性能差
>
> 良好的数据库设计
>
> - 节省内存空间
> - 保证数据库的完整性
> - 方便我们开发系统

**软件开发中关于数据库的设计**

- 分析需求:分析业务和需要处理的数据库的需求
- 概要设计:设计关系图E-R图

**设计数据库的步骤:(个人博客)**

- 收集信息,分析需求

```sql
-- 用户表(用户登录注销,用户个人信息,写博客,创建分类)

-- 分类表 (文章分类,谁创建的)

-- 文章表 (文章的信息)

-- 评论表

-- 友链表 (友链信息)

-- 自定义表 (系统信息,某个关键的字,或者一些主题)    key:value

-- 说说表(发表心情)
```

- 标识实体(把需求落地到每个字段)
- 标识实体之间的关系

```sql
写博客    user --> blog
创建分类   user --> categary
关注:     user --> user
友链:     links
评论  user-user-blog
```



## [三大范式](https://www.cnblogs.com/wsg25/p/9615100.html)

**为什么需要数据规范化**

- 信息重复
- 更新异常
- 插入异常
  - 无法正常显示信息
- 删除异常
  - 丢失有效的信息

### 第一范式（1NF）

原子性：保证每个列不可以再分

举例说明：

![img](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/1218459-20180909201651535-1215699096.png)

在上面的表中，“家庭信息”和“学校信息”列均不满足原子性的要求，故不满足第一范式，调整如下：

![img](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/1218459-20180909202243826-1032549277.png)

可见，调整后的每一列都是不可再分的，因此满足第一范式（1NF）；

### 第二范式（2NF）

前提：满足第一范式

每张表只表示一件事



### 第三范式（3NF）

前提：满足第一范式

前提：满足第二范式

第三范式需要确保数据表中的每一列数据都和主键直接相关，而不能间接相关。

规范性 和 性能的问题

关联查询的表不要超过三张

考虑商业化的需求和目标，（成本，用户体验！）数据库的性能更加重要
在规范性能的问题的时候，需要适当考虑一下规范性！
故意给某些表增加一些冗余的字段。（从多表查询中变为单表查询）
故意增加一些计算列（从大数据量降低为小数据量的查询：索引）

# JDBC(重点)

[**jdbc介绍**](https://baike.baidu.com/item/JDBC%E9%A9%B1%E5%8A%A8%E7%A8%8B%E5%BA%8F/20866650?fr=aladdin)

## 数据库驱动

> 驱动:声卡(可以发出声音),显卡,数据库
>
> SUN公司为了简化开发人员的(对数据库的统一）操作，提供了一个(Java操作数据库的）规范，俗称JDBC
>
> 这些规范的实现由具体的厂商去做!
>
> 对于开发人员来说，只需要掌握JDBC接口的操作即可

**下载驱动**

| 名称      | 地址                                                         |
| --------- | ------------------------------------------------------------ |
| maven下载 | [link](https://mvnrepository.com/artifact/mysql/mysql-connector-java) |
| 官网下载  | [link](https://www.runoob.com/java/java-mysql-connect.html)  [历史版本下载]([MySQL ：： 下载 MySQL Connector/J （存档版本）](https://downloads.mysql.com/archives/c-j/)) |

## 第一个JDBC程序

### 对应代码 

[master分支](https://gitlab.com/yly_java_projects/jdbc_test)

### **创建测试数据库**

```sql
CREATE DATABASE jdbcStudy CHARACTER SET utf8 COLLATE utf8_general_ci;

USE jdbcStudy;

CREATE TABLE `users`(
id INT PRIMARY KEY,
NAME VARCHAR(40),
PASSWORD VARCHAR(40),
email VARCHAR(60),
birthday DATE
);

INSERT INTO `users`(id,NAME,PASSWORD,email,birthday)
VALUES(1,'zhansan','123456','zs@sina.com','1980-12-04'),
(2,'lisi','123456','lisi@sina.com','1981-12-04'),
(3,'wangwu','123456','wangwu@sina.com','1979-12-04')
```



### **创建一个普通项目**

**导入数据库驱动**

**编写测试代码**

```java
package com.yao.lesson01;

import java.sql.*;
//import java.sql.Connection;
//import java.sql.DriverManager;
//import java.sql.ResultSet;
//import java.sql.Statement;

/**
 * 我的第一个jdbc程序
 */
public class JdbcFirstDemo {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        // 1.加载驱动-jdbc jar包需要鼠标右键 add builder添加
        Class.forName("com.mysql.cj.jdbc.Driver");//固定写法,加载驱动
        // 2. 用户信息和url
        String url = "jdbc:mysql://localhost:3306/jdbcStudy?useUnicode=true&characterEncoding=utf8&useSSL=true";
        String username = "root";
        String password = "123456";
        //3.连接成功,数据库对象 Connection 代表数据库
        Connection connection = DriverManager.getConnection(url, username, password);
        //4.执行SQL的对象 Statement 执行sql的对象
        Statement statement = (Statement) connection.createStatement();
        //5.执行SQL的对象去执行SQL,可能存在结果查看返回结果
        String sql = "select * from users";
        ResultSet resultSet = statement.executeQuery(sql);//返回的结果集,结果集中封装了我们全部的查询出来的结果
        while (resultSet.next()) {
            System.out.println("id=" + resultSet.getObject("id"));
            System.out.println("name=" + resultSet.getObject("NAME"));
            //........
        }
        //6.释放连接
        resultSet.close();
        statement.close();
        connection.close();
    }
}
```

步骤总结：

1.加载驱动

2.连接数据库 DriverManager

3.获取执行SQL的对象 Statement

4.获得返回的结果集

5.释放连接

#### DriverManager

```java
// DriverManager.registerDriver(new com.mysql.jdbc.Driver());Class.forName("com.mysql.jdbc.Driver");

// 固定写法Connection 

connection= DriverManager.getConnection(url,name,password);
// connection代表数据库
// 数据库设置自动提交
// 事务提交
// 事务回滚
connection.rollback();
connection.commit();
connection.setAutoCommit();
```

#### URL

```java
String url ="jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&&useSSL=false";
// mysql 默认3306
// 协议://主机地址:端口号/数据库名？参数1&参数2&参数3

// Oracle写法   1521//jdbc:oralce:thin:@localhost:1521:sid
```

#### statement 执行SQL的对象 prepareStatement 执行SQL的对象

```java
String sql="SELECT * FROM users";//编写Sql

statement.executeQuery();//执行查询操作返回Resultset
statement.execute(); //执行所有的sql 
statement.executeUpdate();//更新，插入，删除，返回一个受影响的行数
```

#### ResultSet 查询的结果集，封装了所以的查询结果

> 获得指定的数据类型

```java
ResultSet resultSet = statement.executeQuery(sql);//返回的结果集,结果集中封装了我们全部查询的结果
resultSet.getObject();//在不知道列类型下使用
resultSet.getString();//如果知道则指定使用
resultSet.getInt();
resultSet.getFloat();
//... 获取不同的类型

// 遍历,指针

resultSet.next(); //移动到下一个
resultSet.afterLast();//移动到最后
resultSet.beforeFirst();//移动到最前面
resultSet.previous();//移动到前一行
resultSet.absolute(row);//移动到指定行
```

#### 释放内存

```java
//6. 释放连接
resultSet.close();
statement.close();
connection.close();//耗资源,用完关掉
```

### statement对象

> <font color='red'>Jdbc中的statement对象用于向数据库发送SQL语句，想完成对数据库的增删改查，只需要通过这个对象向数据库发送增删改查语句即可。</font>
>
> Statement对象的`executeUpdate`方法，用于向数据库发送增、删、改的sq|语句， `executeUpdate`执行完后， 将会返回一个整数(即增删改语句导致了数据库几行数据发生了变化)。
>
> `Statement.executeQuery`方法用于向数据库发生查询语句，`executeQuery`方法返回代表查询结果的`ResultSet`对象。

#### CRUD操作-create

>  使用executeUpdate(String sql)方法完成数据添加操作，示例操作：

```java
Statement statement = connection.createStatement();
String sql = "insert into user(...) values(...)";
int num = statement.executeUpdate(sql);
if(num>0) System.out.println("插入成功");
```

#### CRUD操作-delete

> 使用executeUpdate(String sql)方法完成数据删除操作，示例操作：

```java
Statement statement = connection.createStatement();
String sql = "delete from user where id =1";
int num = statement.executeUpdate(sql);
if(num>0) System.out.println("删除成功");
```

#### CURD操作-read

> 使用executeUpdate(String sql)方法完成数据查询操作，示例操作：

```java
Statement statement = connection.createStatement();
String sql = "select * from  user where id =1";
ResultSet rs= statement.executeQuery(sql);
if(rs.next()){
System.out.println("");
}
```

## 代码实现

### 编写工具类

```java
package com.yao.lesson02.utils;


import java.io.IOException;
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;

/**
 * 封装统一增删改查工具类
 */
public class JdbcUtils {
    private static String driver = null;
    private static String url = null;
    private static String username = null;
    private static String password = null;

    static {
        try {
            InputStream in = JdbcUtils.class.getClassLoader().getResourceAsStream("db.properties");
            Properties properties = new Properties();
            properties.load(in);
            driver = properties.getProperty("driver");
            url = properties.getProperty("url");
            username = properties.getProperty("username");
            password = properties.getProperty("password");
            //1.驱动只用加载一次
            Class.forName(driver);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    //2.获取连接
    public static Connection getConnection() throws Exception {
        return DriverManager.getConnection(url, username, password);
    }

    //3.释放资源
    public static void release(Connection conn, Statement st, ResultSet rs) throws SQLException {
        if (rs != null) {
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (st != null) {
            try {
                st.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}

```

**db.properties**

```java
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/jdbcStudy?useUnicode=true&characterEncoding=utf8&useSSL=true
username=root
password=123456
```

### 编写增删改的方法(executeUpdate())

```java
package com.yao.lesson02;

import com.yao.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestDelete {
    public static void main(String[] args) throws SQLException {
        Connection connection = null;
        Statement statement=null;
        ResultSet resultSet=null;
        try {
            connection = JdbcUtils.getConnection();//获取连接
            statement = connection.createStatement();//获取SQL执行对象
            String sql = "delete from users where id=5"; //只需要修改为对应增删改的sql  如果需要替换变量用+号拼接字符即可

            int i = statement.executeUpdate(sql);
            if(i>0){
                System.out.println("删除成功");//只需要修改为对应增删改的文字返回
            }

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JdbcUtils.release(connection,statement,resultSet);
        }

    }
}
```

### 查询(executeQuery())

```java
package com.yao.lesson02;

import com.yao.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestSelect {
    public static void main(String[] args) throws SQLException {
        Connection connection = null;
        Statement statement = null;
        ResultSet resultSet = null;
        try {
            connection = JdbcUtils.getConnection();//获取连接
            statement = connection.createStatement();//获取SQL执行对象
            String sql = "select * from users";

            resultSet = statement.executeQuery(sql); //查询完毕会返回一个结果集
            while (resultSet.next()) {
                System.out.println("姓名是：" + resultSet.getString("NAME"));
                System.out.println("邮箱是：" + resultSet.getString("email"));
                //...
            }

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JdbcUtils.release(connection, statement, resultSet);
        }

    }
}
```

## SQL注入问题

> sql存在漏洞，会被攻击导致数据泄露 SQL会被拼接 or

```java
package com.yao.lesson02;

import com.yao.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class SQL注入 {
    public static void main(String[] args) throws SQLException {

        //login("lisi", "123456"); //正常查询
        // System.out.println("-------------------------");
        login("' or '1=1", "' or '1=1"); //sql注入查询
    }

    //登录业务
    public static void login(String username, String password) throws SQLException {

        Connection conn = null;
        Statement st = null;
        ResultSet rs = null;
        try {
            conn = JdbcUtils.getConnection();//获取连接
            st = conn.createStatement();//获取SQL执行对象
            String sql = "select * from users where `NAME`='" + username + "'  AND `PASSWORD`='" + password + "'";
            System.out.println("原始sql为: " + sql);
            rs = st.executeQuery(sql);//查询完毕返回结果集

            while (rs.next()) {
                System.out.println(rs.getString("NAME"));
                System.out.println(rs.getString("password"));
                System.out.println("==========================================");
            }
            JdbcUtils.release(conn, st, rs);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JdbcUtils.release(conn, st, rs);
        }
    }
}	
```

##  PreparedStatement对象

> PreparedStatement 可以防止SQL注入 ，效率更高。

###  增

```java
package com.yao.lesson03;

import com.yao.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.Date;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class testInsert {
    public static void main(String[] args) throws SQLException {
        Connection conn = null;
        PreparedStatement st = null;

        try {
            conn = JdbcUtils.getConnection();
            //区别
            // 使用 ？占位符 代替参数
            String sql = "INSERT INTO users(`NAME`,`PASSWORD`,`email`,`birthday`) VALUES(?,?,?,?)"; 
            st = conn.prepareStatement(sql);//预编译sql,先写sql,然后不执行
            //手动给参数赋值
            st.setString(1, "测试姓名");
            st.setString(2, "123456");
            st.setString(3, "yaoliuyang@aliyun.com");
            st.setDate(4, Date.valueOf("1997-02-08"));
            int i=st.executeUpdate();
            if (i>0) System.out.println("插入成功");

        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            JdbcUtils.release(conn,st,null);
        }
    }
}

```

### 删

```java
package com.yao.lesson03;

import com.yao.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.Date;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class TestDelete {
    public static void main(String[] args) throws SQLException {
        Connection conn = null;
        PreparedStatement st = null;

        try {
            conn = JdbcUtils.getConnection();
            //区别
            // 使用 ？占位符 代替参数
            String sql = "delete from users where id=?";
            st = conn.prepareStatement(sql);//预编译sql,先写sql,然后不执行
            //手动给参数赋值
            st.setInt(1, 4);
            int i=st.executeUpdate();
            if (i>0) System.out.println("删除成功");

        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            JdbcUtils.release(conn,st,null);
        }
    }
}

```

### 修改

```java
package com.yao.lesson03;

import com.yao.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class TestUpdate {
    public static void main(String[] args) throws SQLException {
        Connection conn = null;
        PreparedStatement st = null;

        try {
            conn = JdbcUtils.getConnection();
            //区别
            // 使用 ？占位符 代替参数
            String sql = "update users set Name=?,password=? where id=?";
            st = conn.prepareStatement(sql);//预编译sql,先写sql,然后不执行
            //手动给参数赋值
            st.setString(1, "测试修改");
            st.setString(2, "12345678");
            st.setInt(3, 3);
            int i = st.executeUpdate();
            if (i > 0) System.out.println("修改成功");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JdbcUtils.release(conn, st, null);
        }
    }
}
```



### 查询

```sql
package com.yao.lesson03;

import com.yao.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class TestSelect {
    public static void main(String[] args) throws SQLException {
        Connection conn = null;
        PreparedStatement st = null;
        ResultSet rs=null;
        try {
            conn = JdbcUtils.getConnection();
            //区别
            // 使用 ？占位符 代替参数
            String sql = "select * from users where id=?";
            st = conn.prepareStatement(sql);//预编译sql,先写sql,然后不执行
            //手动给参数赋值
            st.setInt(1, 3);
            rs = st.executeQuery();
            while (rs.next()){
                System.out.println( rs.getString("NAME"));;
                //...
            }

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JdbcUtils.release(conn, st, rs);
        }
    }
}
```

### sql 注入测试(不会注入成功)

```java
package com.yao.lesson03;

import com.yao.lesson02.utils.JdbcUtils;

import java.sql.*;

public class SQL注入 {
    public static void main(String[] args) throws SQLException {

        //login("123", "123456"); //正常查询
        // System.out.println("-------------------------");
        login("' or '1=1", "' or '1=1"); //sql注入查询 不会注入成功
    }

    //登录业务
    public static void login(String username, String password) throws SQLException {

        Connection conn = null;
        PreparedStatement st = null;
        ResultSet rs = null;
        try {
            conn = JdbcUtils.getConnection();//获取连接
            //PreparedStatement 防止SQL注入的本质,把传递进来的参数当作字符
            //假设其中存在转义字符,就直接忽略, '' 引号会直接转义掉
            String sql = "select * from users where `NAME`= ? AND `PASSWORD`=?";
            st = conn.prepareStatement(sql);//获取SQL执行对象
            System.out.println("原始sql为: " + sql);
            st.setString(1, username);
            st.setString(2, password);
            rs = st.executeQuery();//查询完毕返回结果集

            while (rs.next()) {
                System.out.println(rs.getString("NAME"));
                System.out.println(rs.getString("password"));
                System.out.println("==========================================");
            }
            JdbcUtils.release(conn, st, rs);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JdbcUtils.release(conn, st, rs);
        }
    }
}
```

##  使用IDEA连接数据库

![MySQL学习笔记（狂神说）_数据库_14](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/e16c352913860481d862f28346b602d5.png)

> 连接成功后，可以选择数据库	

![MySQL学习笔记（狂神说）_sql_15](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/3f16797cfbb1eba846514b65bda22b92.png)

> 双击数据库

![MySQL学习笔记（狂神说）_mysql_16](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/747cafcb855bd935813adce8c1384ff2.png)

> 更新数据、

![MySQL学习笔记（狂神说）_数据_17](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/ac7025a3f87f9f3b1e05c057c45b3ab5.png)

> 编写sql代码的地方

![MySQL学习笔记（狂神说）_数据库_18](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/20141d68c93570f9c58da689dcb7c551.png)

## Jdbc事务

> <font color='red'>要么都成功，要么都失败</font>
>
> ACID原则
>
> 原子性：要么全部完成，要么都不完成
>
> 一致性：结果总数不变
>
> 隔离性：多个进程互不干扰
>
> 持久性：一旦提交不可逆，持久化到数据库了
>
>  隔离性的问题：
>
> 脏读： 一个事务读取了另一个没有提交的事务
>
> 不可重复读：在同一个事务内，重复读取表中的数据，表发生了改变
>
> 虚读（幻读）：在一个事务内，读取到了别人插入的数据，导致前后读出来的结果不一致

### 代码实现

**插入账户数据库**

```sql
CREATE TABLE `account` (
  `id` int NOT NULL AUTO_INCREMENT,
  `name` varchar(20) NOT NULL,
  `money` decimal(9,2) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
-- 插入数据
INSERT INTO `account` (`name`,`money`) values ('A',2000),('B',10000)
```

**代码示例**

> 开启事务`conn.setAutoCommit(false);`
> 一组业务执行完毕，提交事务
> 可以在catch语句中显示的定义回滚，但是默认失败会回滚

```java
package com.yao.lesson04;

import com.yao.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

/**
 * 模拟jdbc事务:模拟事务失败场景
 */
public class TestTransaction2 {


    public static void main(String[] args) throws SQLException {
        Connection conn = null;
        PreparedStatement st = null;
        ResultSet rs = null;

        try {
            conn = JdbcUtils.getConnection();
            //关闭数据库的自动提交,自动会开启事务
            conn.setAutoCommit(false);


            String sql1 = "update account set money=money-100 where name='A'";
            st = conn.prepareStatement(sql1);//预编译sql,先写sql,然后不执行
            st.executeUpdate();

           // int x=1/0; //执行到此处一定会失败中断

            String sql2 = "update account set money=money+100 where name='B'";
            st = conn.prepareStatement(sql2);//预编译sql,先写sql,然后不执行
            st.executeUpdate();

            //业务完毕,提交事务
            conn.commit();
            System.out.println("修改成功");

        } catch (Exception e) {
            conn.rollback(); //如果失败回滚事务
            e.printStackTrace();
        } finally {
            JdbcUtils.release(conn, st, rs);
        }
    }
}
```

## 数据库连接池

数据库连接-- 执行完毕--释放

连接--释放 十分浪费系统资源

**池化技术:准备一些预先的资源,过来就连接预先准备好的**

---开门--业务员:等待 --服务--

> 常用连接数 100
>
> 最少连接数：100
>
> 最大连接数 ： 120 业务最高承载上限
>
> 排队等待，
>
> 等待超时：100ms
>
> 编写连接池，实现一个接口 DateSource

**编写连接池,实现一个接口DataSource**



> 开源数据源实现(拿来即用)

- DBCP
- C3P0
- Druid: 阿里巴巴

使用了这些数据库连接池之后,我们在项目开发种就不需要编写数据库的代码

###  **DBCP**

**对应代码文件配置目录**

![image-20230322095000423](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20230322095000423.png)



**DBCP包相关下载地址**

| apache官网下载 | [link](https://commons.apache.org/proper/)                   |
| -------------- | ------------------------------------------------------------ |
| 参考文档       | [link](https://blog.csdn.net/qq_45523411/article/details/121517140) |

> 需要用到的**jar包**  
>
> [commons-dbcp2-2.9.0.jar](https://commons.apache.org/proper/commons-dbcp/download_dbcp.cgi)
>
> ![image-20230322094447729](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20230322094447729.png)
>
> [commons-logging-1.2.jar](https://commons.apache.org/proper/commons-logging/download_logging.cgi)
>
> ![image-20230322094423215](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20230322094423215.png)
>
> [commons-pool2-2.11.1.jar](https://commons.apache.org/proper/commons-pool/download_pool.cgi)
>
> ![image-20230322094553082](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20230322094553082.png)

#### 定义配置文件(dbcpconfig.properties)

```java
#连接设置,这里面的名字是DBCP数据源中定义好的
driverClassName=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/jdbcStudy?useUnicode=true&characterEncoding=utf8&useSSL=true
username=root
password=123456
#<!-- 初始化连接-->
initialSize=10
#最大连接数量
maxActive=50
#最大空闲连接
maxIdle=20
#最小空闲连接
minIdle=5
#超时等待时间以毫秒为单位/s
maxWait=60000
#JDBC驱动建立连接时附带的连接属性属性的格式必须为这样：[属性名=property;]
#注意：“user” 与 “password” 两个属性会被明确地传递，因此这里不需要包含他们。
connectionProperties=useUnicode=true;characterEncoding=utf8
#指定由连接池所创建的连接的自动提交（auto-commit）状态。
defaultAutoCommit=true
#driver default 指定由连接池所创建的连接的事务级别（TransactionIsolation）。
#可用值为下列之一：（详情可见javadoc。）NONE,READ_UNCOMMITTED, READ_COMMITTED, REPEATABLE_READ, SERIALIZABLE
defaultTransactionIsolation=READ_UNCOMMITTED
```

#### 定义连接池工具类文件(JdbcDbcpUtils)

```java
package com.yao.lesson05.utils;


import org.apache.commons.dbcp2.BasicDataSourceFactory;

import javax.sql.DataSource;
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;

/**
 * 封装统一增删改查工具类
 */
public class JdbcDbcpUtils {

    private static DataSource dataSource = null;

    static {
        try {
            InputStream in = JdbcDbcpUtils.class.getClassLoader().getResourceAsStream("dbcpconfig.properties");
            Properties properties = new Properties();
            properties.load(in);
            //创建数据源
            dataSource = BasicDataSourceFactory.createDataSource(properties);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    //2.获取连接
    public static Connection getConnection() throws Exception {
        return dataSource.getConnection();//从数据源中获取连接
    }

    //3.释放资源
    public static void release(Connection conn, Statement st, ResultSet rs) throws SQLException {
        if (rs != null) {
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (st != null) {
            try {
                st.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

#### 测试新增逻辑

```java
package com.yao.lesson05;

import com.yao.lesson05.utils.JdbcDbcpUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestDbcp {
    public static void main(String[] args) throws SQLException {
        Connection connection = null;
        Statement statement = null;
        ResultSet resultSet = null;
        try {
            connection = JdbcDbcpUtils.getConnection();//获取连接
            statement = connection.createStatement();//获取SQL执行对象
            String sql = "INSERT INTO users(`NAME`,`PASSWORD`,`email`,`birthday`)" +
                    "VALUES('sanjin','123456','233223@qq.com','2020-01-01')";

            int i = statement.executeUpdate(sql);
            if (i > 0) {
                System.out.println("插入成功");
            }

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JdbcDbcpUtils.release(connection, statement, resultSet);
        }
    }
}
```

## c3p0

