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

### 分页()

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























