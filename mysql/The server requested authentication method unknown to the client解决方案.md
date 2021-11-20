# 说明

> 晚上连接mysql8的时候项目报错**The server requested authentication method unknown to the client**
>
> 百度了一番得以解决

# [解决方案](https://blog.51cto.com/u_15102978/2639358)

```shell
mysql - uroot - p
# 显示数据库
mysql> show databases;
# 选择 数据库
mysql> use mysql;
# 显示表名 
mysql> show tables;

# 修改身份验证插件
ALTER USER root@localhost IDENTIFIED WITH mysql_native_password BY '123456';

FLUSH PRIVILEGES; #刷新
```

