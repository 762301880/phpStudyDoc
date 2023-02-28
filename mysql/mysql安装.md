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

```



# 压缩包形式安装mysql(windows平台下强烈推荐使用)

> 压缩包方式安装好处就是想删就删，因为用程序安装会修改系统注册表会导致卸载不干净的情况

## 资料

| 名称                     | 地址                                                         |
| ------------------------ | ------------------------------------------------------------ |
| mysql官方下载地址        | [link](https://downloads.mysql.com/archives/community/)  [MySQL ：： 下载 MySQL 社区服务器](https://dev.mysql.com/downloads/mysql/) |
| 清华大学开源镜像站-mysql | [link](https://mirrors.tuna.tsinghua.edu.cn/mysql/downloads/MySQL-5.7/)  [点击下载](https://mirrors.tuna.tsinghua.edu.cn/mysql/downloads/MySQL-5.7/mysql-5.7.36-winx64.zip) |
| 官方说明文档             | [MySQL ：： MySQL 8.0 参考手册 ：： 2.3.4.1 提取安装归档文件](https://dev.mysql.com/doc/refman/8.0/en/windows-extract-archive.html) |
| 菜鸟教程mysql安装方式    | [link](https://www.runoob.com/mysql/mysql-install.html)      |

## windows-压缩包安装

> 官网下载[mysql](https://downloads.mysql.com/archives/community/)版本,这里下载**64位**压缩版本,最好是**5.7版本**
>
> 由于官网下载速度很慢所以我们到开源镜像站下载,**[清华大学开源镜像站-mysql](https://mirrors.tuna.tsinghua.edu.cn/mysql/downloads/MySQL-5.7/)**

![image-20220709150956479](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220709150956479.png)

### **解压软件包**

> 解压后的目录,项目启动后会在里面自动生成一个**data**目录

![image-20220709152617916](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220709152617916.png)



### **创建my.ini文件**

> 在解压目录创建my.ini文件并添加内容如下

```shell
[mysqld]
# 设置3306端口
port=3306
character-set-server=UTF8MB4

# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB

[mysql]
# 设置mysql客户端默认字符串
default-character-set=UTF8MB4
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=UTF8MB4
```

### 配置系统环境变量

> - 在【我的电脑】右键
>
> - 选择【高级系统设置】
>
> - 选择【高级】-》【环境变量】
>
> 配置变量完成之后可以**cmd**中 使用`echo %MYSQL_HOME%` 命令查看是否配置成功

![image-20230228114332181](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20230228114332181.png)

**配置系统环境**

> 将`MYSQL_HOME`添加到`PATH`环境变量

![image-20230228114502168](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20230228114502168.png)

### 安装服务

> <font color='red'>使用管理员权限进入DOS</font>，在cmd中，进入解压目录下的bin目录依次执行以下命令：

```shell
# 对mysql进行初始化，请注意，这里会生产一个临时密码，后边要使用这个临时密码(注意这个临时密码不要弄丢了下面要用到)
  mysqld --initialize --user=mysql --console
  
# 安装mysql服务
 mysqld --install 


# 启动mysql服务
 net start mysql

# 登录mysql，这里需要使用之前生产的临时密码
 mysql -uroot –p   

# 修改root用户密码
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456’;

# 修改root用户权限
create user 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';

```

**示例**

```shell
# 对mysql进行初始化，请注意，这里会生产一个临时密码，后边要使用这个临时密码(注意这个临时密码不要弄丢了下面要用到)
C:\Windows\system32>  mysqld --initialize --user=mysql --console
2023-02-28T03:31:46.255706Z 0 [System] [MY-013169] [Server] D:\Program Files\mysql-8.0.32-winx64\bin\mysqld.exe (mysqld 8.0.32) initializing of server in progress as process 15936
2023-02-28T03:31:46.316746Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2023-02-28T03:31:53.175973Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2023-02-28T03:31:57.707397Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: !SwuO=#*D53j
# !SwuO=#*D53j 就是临时密码

# 安装mysql服务
C:\Windows\system32> mysqld --install
Install/Remove of the Service Denied!

# cmd 中启动mysql服务
C:\Windows\system32>   net start mysql
MySQL 服务正在启动 ...
MySQL 服务已经启动成功。


# 进入mysql
C:\Users\铺先生技术研发中心>mysql -u root -p
Enter password: ******
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.32 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
# 修改root用户密码
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';
Query OK, 0 rows affected (0.02 sec)
# 修改root用户权限
mysql> create user 'root'@'%' IDENTIFIED WITH mysql_native_password BY  '123456';
Query OK, 0 rows affected (0.02 sec)
# 刷新使其生效
mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.03 sec)
```

**补充**

**如果期间配置失败想重新配置**

> 例如不小心忘记保存临时密码的情况
>
> 1. 先删除`mysqld`进程
> 2. 再到安装目录删除`data`目录重新操作安装命令即可

![image-20230228113823521](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20230228113823521.png)

# [linux安装mysql](https://www.w3cschool.cn/mysql/mysql-install.html)
