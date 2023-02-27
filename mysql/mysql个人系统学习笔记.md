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

## 1.1 为什么学习数据库

1. 岗位需求
2. 现在的世界,大数据时代~,得数据者得天下
3. 被迫需求:存数据
4. <font color='red'>数据库是所有软件体系中最核心的存在</font> DBA

## 1.2 什么是数据库

数据库(DB,DataBase)

概念:数据仓库,软件,安装在操作系统(windows,linux,mac...)之上! SQL,可以存储大量的数据,500万!

作用: 存储数据,管理数据

## 1.3  数据库分类

关系型数据库:(SQL)

- Mysql,Oracle,Sql Server,DB2,SQLlite
- 通过表和表之间,行和列之间的关系进行数据的存储,学员信息表, 考勤表,....



非关系型数据库:(NoSql) Not Only

- Redis,MongDB
- 非关系型数据库,对象存储,通过对象的自身的属性来决定

<font color='red'>DBMS(数据库管理系统)</font> 

- 数据库的管理软件,科学有效的管理我们的数据。维护和获取数据;
- MySQL,数据库管理系统!

## 1.4 [mysql简介](https://baike.baidu.com/item/MySQL/471251?fr=aladdin)

MySQL是一个**[关系型数据库管理系统](https://baike.baidu.com/item/关系型数据库管理系统/696511?fromModule=lemma_inlink)**

**历史 :** 由瑞典MySQL AB 公司开发，目前属于 Oracle 旗下产品。

MySQL是最好的 [RDBMS](https://baike.baidu.com/item/RDBMS/1048260?fromModule=lemma_inlink) (Relational Database Management System，关系数据库管理系统) 应用软件之一。

开源的数据库软件~  

体积小、速度快、总体拥有成本低

官网[www.mysql.com](https://www.mysql.com/)

mysql官网下载地址 [https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)

## 1.5 安装mysql

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











