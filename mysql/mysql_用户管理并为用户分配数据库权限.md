

# 说明&资料

## 说明

> mysql超级用户下有很多特殊的数据库,例如**mysql** 里面存放着用户的信息，我们不希望开发人员可以看到这个库，
>
> 怕他误删，或者不想给开发人员超级用户账户信息，又或者只想给他设置一个只读数据库，这时候就可以给他设置一个
>
> 新账号并设置权限

## 资料

| 名称                   | 地址                                                         |
| ---------------------- | ------------------------------------------------------------ |
| 第三方博客             | [link](https://blog.csdn.net/weixin_30516835/article/details/113191074?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-113191074-blog-111882700.pc_relevant_antiscanv3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-113191074-blog-111882700.pc_relevant_antiscanv3&utm_relevant_index=2) |
| mysql数据库的grant授权 | [菜鸟教程-MySQL Grant 命令](https://www.runoob.com/note/19873)   [百度文库](https://wenku.baidu.com/view/51ef763a5aeef8c75fbfc77da26925c52cc591e2.html) |

## mysql5.7 创建用户并修改权限 

```php
 create  user  '用户名'@'ip地址'  identified by  '密码';
 grant select, insert, update, delete on 数据库名称.* to 用户名@'ip地址'; # 设置所有权限

 FLUSH PRIVILEGES; #刷新权限

# 只创建只读数据库

CREATE USER 'readonly_user'@'localhost' IDENTIFIED BY 'your_password';
GRANT SELECT ON database_name.* TO 'readonly_user'@'localhost';
FLUSH PRIVILEGES;
```





#  参考

MySQL是一种流行的关系型数据库管理系统，可以使用以下命令为用户赋予权限：

1. 创建用户：

   ```mysql
   CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
   ```

   这将创建一个名为’username’的用户，只能从本地主机访问，并使用指定的密码进行身份验证。

2. 授予全局权限：

   ```mysql
   GRANT ALL PRIVILEGES ON *.* TO 'username'@'localhost';
   ```

   这将授予用户’username’在所有数据库和所有表上的全部权限。

3. 授予特定数据库权限：

   ```mysql
   GRANT ALL PRIVILEGES ON dbname.* TO 'username'@'localhost';
   ```

   这将授予用户’username’在指定的数据库（dbname）上的全部权限。

4. 授予特定表权限：

   ```mysql
   GRANT SELECT, INSERT, DELETE ON dbname.tablename TO 'username'@'localhost';
   ```

   这将授予用户’username’在指定数据库（dbname）的特定表（tablename）上的SELECT、INSERT和DELETE权限。

5. 刷新权限：

   ```mysql
   FLUSH PRIVILEGES;
   ```

   在修改权限后，使用此命令刷新权限，以使更改生效。

请注意，上述命令中的’localhost’可以替换为其他主机名或 IP 地址，以允许从其他主机访问数据库。另外，'*'代表所有数据库或所有表，你可以根据需求进行调整。

以上是MySQL中常用的赋予用户权限的命令。详细的权限管理内容可以参考MySQL官方文档。

**示例**

```shell
# 创建用户
CREATE USER 'yao_test_backup'@'%' IDENTIFIED BY 'yao123456';
# 分配所有权限
GRANT ALL PRIVILEGES ON housekeeping.* TO 'yao_test_backup'@'%';


```

