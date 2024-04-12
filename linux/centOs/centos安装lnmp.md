# 一、资料

## 1.1阿里云`nginx`搭建`lnmp`[文档](https://help.aliyun.com/document_detail/97251.html)

```shell
https://help.aliyun.com/document_detail/97251.html
```

# 二、安装`nginx`

## 2.1 使用`yum`命令安装

```shell
yum install -y nginx
```

- 启动nginx

```shell
service nginx start 
```



- 查看是否安装成功

```shell
[root@VM-24-20-centos conf.d]# nginx -v
nginx version: nginx/1.20.1
```

## 2.2 配置nginx

### 2.1运行以下命令查看`Nginx`配置文件的默认路径。

```shell
cat /etc/nginx/nginx.conf
```

### 2.2在`http`大括号内，查看`include`配置项。即配置文件的默认路径。

![conf](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/p130116.png)

### 2.3在配置文件的默认路径下，备份默认配置文件。

```shell
cd /etc/nginx/conf.d
cp default.conf default.conf.bak
```

# 三、安装 `mysql5.7`

## 3.1 安装`MySQL`

1. 运行以下命令更新YUM源。

   ```shell
   rpm -Uvh  http://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
   ```

2. 运行以下命令安装`MySQL`

   **说明** 如果您使用的操作系统内核版本为`el8`，可能会提示报错信息No match for argument。您需要先运行命令**yum module disable mysql**禁用默认的mysql模块，再安装MySQL。

   ```shell
   yum -y install mysql-community-server
   ```

3. 运行以下命令查看MySQL版本号。

   ```shell
   mysql -V
   ```

   返回结果如下所示，表示MySQL安装成功。

   ```shell
   mysql  Ver 14.14 Distrib 5.7.28, for Linux (x86_64) using  EditLine wrapper
   ```

4. 运行以下命令启动MySQL。

   ```shell
   systemctl start mysqld
   ```

5. 运行以下命令设置开机启动MySQL。

   ```shell
   systemctl enable mysqld
   systemctl daemon-reload
   ```

## 3.2 配置MySQL

1 . 运行以下命令查看/var/log/mysqld.log文件，获取并记录root用户的初始密码。

```shell
grep 'temporary password' /var/log/mysqld.log
```

- 显示如下

```shell
# fktgczag+0qQ 就是密码
2021-06-29T08:20:10.524041Z 1 [Note] A temporary password is generated for root@localhost: fktgczag+0qQ
```

2. 运行以下命令配置MySQL的安全性。

```
mysql_secure_installation
```

- 使用数据库报错

```shell
mysql> show databases;
# 在执行此语句之前，必须使用ALTER USER语句重置密码(会报错提醒让我们修改密码)
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
# 修改密码
alter user user() identified by "asda355555523113"; # 随便设置一个
```

- 如果设置远程链接可以查看我的另一篇[博客](https://www.cnblogs.com/yaoliuyang/p/13266376.html)

```shell
https://www.cnblogs.com/yaoliuyang/p/13266376.html
```



安全性的配置包含以下五个方面：

1. 重置root账号密码。

   ```shell
   Enter password for user root: #输入上一步获取的root用户初始密码
   The 'validate_password' plugin is installed on the server.
   The subsequent steps will run with the existing configuration of the plugin.
   Using existing password for root.
   Estimated strength of the password: 100 
   Change the password for root ? (Press y|Y for Yes, any other key for No) : Y #是否更改root用户密码，输入Y
   New password: #输入新密码，长度为8至30个字符，必须同时包含大小写英文字母、数字和特殊符号。特殊符号可以是()` ~!@#$%^&*-+=|{}[]:;‘<>,.?/
   Re-enter new password: #再次输入新密码
   Estimated strength of the password: 100 
   Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : Y
   ```

2. 输入Y删除匿名用户账号。

   ```shell
   By default, a MySQL installation has an anonymous user, allowing anyone to log into MySQL without having to have a user account created for them. This is intended only for testing, and to make the installation go a bit smoother. You should remove them before moving into a production environment.
   Remove anonymous users? (Press y|Y for Yes, any other key for No) : Y  #是否删除匿名用户，输入Y
   Success.
   ```

3. 输入Y禁止root账号远程登录。

   ```shell
   Disallow root login remotely? (Press y|Y for Yes, any other key for No) : Y #禁止root远程登录，输入Y
   Success.
   ```

4. 输入Y删除test库以及对test库的访问权限。

   ```shell
   Remove test database and access to it? (Press y|Y for Yes, any other key for No) : Y #是否删除test库和对它的访问权限，输入Y
   - Dropping test database...
   Success.
   ```

5. 输入Y重新加载授权表。

   ```shell
   Reload privilege tables now? (Press y|Y for Yes, any other key for No) : Y #是否重新加载授权表，输入Y
   Success.
   All done!
   ```

## 3.3 更多mysql配置请查阅[官方文档](https://dev.mysql.com/doc/refman/5.7/en/mysql-secure-installation.html?spm=a2c4g.11186623.2.25.32626a94kN53LB)

