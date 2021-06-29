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

- phpstudy拷贝文件  [laravel nginx需要优雅链接配置](https://learnku.com/docs/laravel/8.x/installation/9354#62e0b5)

```shell
server {
        listen        80;
        server_name  www.cs.com; # 填写自己服务的名字
        root   /WWW/work_project/php-api-admin/public; #填写自己项目的地址
       # laravel nginx需要优雅链接配置      
       location / {
          try_files $uri $uri/ /index.php?$query_string;
        }
        location ~ \.php(.*)$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_split_path_info  ^((?U).+\.php)(/?.+)$;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            fastcgi_param  PATH_INFO  $fastcgi_path_info;
            fastcgi_param  PATH_TRANSLATED  $document_root$fastcgi_path_info;
            include     fastcgi_params;
        }
}
```

- laravel 学院[nginx配置](https://learnku.com/docs/laravel/8.x/deployment/9359#nginx)

```shell
server {
    listen 80;
    server_name example.com;# 填写自己服务的名字
    root /srv/example.com/public; # 填写自己项目的地址

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```



# 二、ubuntu安装`msyql`

参考资料[第三方博客](https://blog.csdn.net/weixx3/article/details/80782479)

## 2.1 安装mysql

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

## 2.4 navicat mysql客户端链接mysql

`记住navicat的主机一定要填写   127.0.0.1`

> 1. 问题，以上安装之后使用navicat发现就是无法安装mysql
> 2. 参考[资料](https://m.linuxidc.com/Linux/2013-11/92763.htm)

`解决方案`

- 用创建安装时候默认创建的密码登陆

```shell
#进入 /etc/mysql/ 打开debian.cnf 这里保存着相关的用户名与密码
root@benben:/etc/mysql# cd /etc/mysql/ 
root@benben:/etc/mysql# cat debian.cnf 

# 显示如下
[client] # 这里就是我们链接需要的用户名&密码
host     = localhost # 127.0.0.1
user     = debian-sys-maint
password = g0aju1KPWK0whF2A # 此处就是通过apt命令安装mysql系统给我们设置的用户名&密码
socket   = /var/run/mysqld/mysqld.sock
[mysql_upgrade]
host     = localhost
user     = debian-sys-maint
password = g0aju1KPWK0whF2A
socket   = /var/run/mysqld/mysqld.sock
```

- 创建新用户登陆(适用于mysql8)

  [参考资料](https://blog.csdn.net/Jatal/article/details/104273439)

```shell
# 1.先采用root 方式或者上一种默认账户与密码的方式进入mysql
use mysql;# 选择数据库
#创建新的用户 by 后面跟密码
create user 'yaoliuyang'@'localhost' identified by '123456';
# 设置权限也就是将数据库中的权限字段设置为Y 
grant all privileges on *.* to 'yaoliuyang'@'localhost';
# 刷新MySQL系统权限相关表
flush privileges;

update mysql.user set authentication_string=password('123456') where user='root' and host='localhost' 
```

- 重新设置root账户登陆

参考[博客](https://blog.csdn.net/naffan/article/details/115720337)

```shell
关闭mysql服务。
找到mysql的安装位置，并进入bin文件夹，会发现mysqld运行文件
运行 mysqld –skip-grant-tables #跳过登录检验环节直接启动mysql
use mysql;
ALTER USER 'root'@'localhost' IDENTIFIED BY '你的密码' PASSWORD EXPIRE NEVER; #修改密码规则
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码'; #更新用户密码
flush privileges; #刷新权限
```



## 2.5卸载mysql

- apt安装使用命令卸载

```shell
sudo apt remove mysql-*
```

