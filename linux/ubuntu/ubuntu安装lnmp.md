# 资料

| 资料名称       | 地址         |
| -------------- | ------------ |
| 阿里云帮助文档 | [点我跳转]() |
|                |              |



# 一、安装nginx

[nginx中文文档](https://www.nginx.cn/doc/)

##  1.1安装nginx

```shell
sudo apt install nginx
```

- 查看是否安装成功

```shell
nginx -v
```

## 1.2建立自己的服务



# 二、ubuntu安装`msyql`

## 2.1 安装msyql

- 安装mysql服务

```shell
sudo apt install mysql-server  
```

##  2.2 初始化配置

```shell
sudo mysql_secure_installation
```

- 配置项

```shell
# 1 验证密码插件可以用来测试密码
VALIDATE PASSWORD PLUGIN can be used to test passwords...
# 按“y| y”选择“是”，按“否”选择“N”
Press y|Y for Yes, any other key for No: N (我的选项)

# 2 请在这里设置root的密码…
Please set the password for root here... 
# 重置密码此处请输入两次密码
New password: (输入密码)       
Re-enter new password: (重复输入)

# 3 默认情况下，MySQL安装有一个匿名用户，允许任何人不需要登录MySQL一个为他们创建的用户帐户…
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them...
# 删除匿名用户?(按y| y为Yes，按其他键为No)
Remove anonymous users? (Press y|Y for Yes, any other key for No) : N (我的选项)

# 4通常，根应该只允许从“localhost”。这就保证了别人猜不到网络上的root密码…
Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network...
# 禁止root远程登录?(按y| y为Yes，按其他键为No)
Disallow root login remotely? (Press y|Y for Yes, any other key for No) : Y (我的选项)

# 5默认情况下，MySQL有一个名为test的数据库任何人都可以访问……
By default, MySQL comes with a database named 'test' that
anyone can access...
# 删除测试数据库并访问它?(按y| y为Yes，按其他键为No)
Remove test database and access to it? (Press y|Y for Yes, any other key for No) : N (我的选项)

# 6重新加载特权表将确保所有更改到目前为止所做的将立即生效。 
Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.
# 现在重新加载特权表?(按y| y为Yes，按其他键为No)
Reload privilege tables now? (Press y|Y for Yes, any other key for No) : Y (我的选项)
```

## 2.3登陆mysql

- 普通用户登陆

```shell
sudo mysql -uroot -p
# 这里输入你设置的密码即可
Enter password:
```

- root 用户免密码登陆

```shell
sudo su root
# 切换root用户直接输入mysql即可
mysql
```

