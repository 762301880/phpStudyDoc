# mysql

## 说明

> 今天在容器内部使用了**swoole**,但是使用**PDO扩展**连接本地mysql发现报错
>
> ` Uncaught PDOException: SQLSTATE[HY000] [2002] Connection refused in /data/swoole/Mysql.php:160
> Stack trace:` **连接拒绝**错误

## [解决方案](https://blog.csdn.net/ouyang_zhen/article/details/117362728)

**先看代码**

> 如下所示我想容器内部执行**php Mysql.php**的时候测试**swoole-Mysql**客户端的代码，但是容器内部无法连接本地的**mysql**

```php
class Mysql
{
    public function go()
    {
        //    // 10k pdo and mysqli read
        Runtime::enableCoroutine();
        $s = microtime(true);
        for ($c = 50; $c--;) {
            Coroutine::create(function () {
                # 问题就出在127.0.0.1 容器内部执行会去容器自己本地查询这个地址连接mysql找不到所以报错
                $pdo = new PDO('mysql:host=127.0.0.1;dbname=laravel_study;charset=utf8', 'root', '123456');
                $statement = $pdo->prepare('SELECT * FROM `stu`');
                for ($n = 100; $n--;) {
                    $statement->execute();
                    assert(count($statement->fetchAll()) > 0);
                }
            });
        }
//        for ($c = 50; $c--;) {
//            Coroutine::create(function () {
//                $mysqli = new Mysqli('127.0.0.1', 'root', '123456', 'laravel_study');
//                $statement = $mysqli->prepare('SELECT `id` FROM `stu`');
//                for ($n = 100; $n--;) {
//                    $statement->bind_result($id);
//                    $statement->execute();
//                    $statement->fetch();
//                    assert($id > 0);
//                }
//            });
//        }
        echo '用时 ' . (microtime(true) - $s) . ' s';
    }
}

$mysql = new Mysql();
$mysql->go();
```

## **采用本地ip链接本地mysql**

> 想着我这里采用的不是远程**linux**服务器**而是本地开发环境**所以采用本地**ip**地址链接mysql

```php
D:\>ipconfig

Windows IP 配置


以太网适配器 vEthernet (Default Switch):

   连接特定的 DNS 后缀 . . . . . . . :
   本地链接 IPv6 地址. . . . . . . . : fe80::c1b3:ea71:e40b:9362%25
   IPv4 地址 . . . . . . . . . . . . : 172.19.0.1      # 这个就是本地ip地址
   子网掩码  . . . . . . . . . . . . : 255.255.240.0
   默认网关. . . . . . . . . . . . . :

以太网适配器 以太网:

   连接特定的 DNS 后缀 . . . . . . . :
   本地链接 IPv6 地址. . . . . . . . : fe80::ec67:a720:163:900c%14
   IPv4 地址 . . . . . . . . . . . . : 10.10.50.230
   子网掩码  . . . . . . . . . . . . : 255.255.255.0
   默认网关. . . . . . . . . . . . . : 10.10.50.254

以太网适配器 vEthernet (WSL):

   连接特定的 DNS 后缀 . . . . . . . :
   本地链接 IPv6 地址. . . . . . . . : fe80::9473:5b98:d7dd:3a46%32
   IPv4 地址 . . . . . . . . . . . . : 172.30.176.1
   子网掩码  . . . . . . . . . . . . : 255.255.240.0
   默认网关. . . . . . . . . . . . . :
```



## [设置MySQL外网连接](https://blog.csdn.net/weixin_45685353/article/details/105135277)

**mysql -h 主机名 (ip) -u 用户名 -P 端口号 -p**

```mysql
# 登录mysql
D:\>mysql -h localhost -u root -p
Enter password: ******
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 368
Server version: 5.7.25-log MySQL Community Server (GPL)

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
#.........

# 修改本地链接为所有地址都可以链接 
# 显示数据库
mysql> show databases;
# 选择 数据库
mysql> use mysql;
# 显示表名 
mysql> show tables;

mysql> select host,user from user;
+-----------+---------------+
| host      | user          |
+-----------+---------------+
| localhost | mysql.session |
| localhost | mysql.sys     |
| localhost | root          |
+-----------+---------------+

mysql> update user set host = "%" where user = "root"; # 修改本地地址可以远程访问
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> FLUSH PRIVILEGES; #刷新
Query OK, 0 rows affected (0.00 sec) 
```

## **最终处理**

> 将**PDO**连接地址修改为**本地IPv4 地址**即可

```php
    $pdo = new PDO('mysql:host=172.19.0.1;dbname=laravel_study;charset=utf8', 'root', '123456');
```



## 小bug处理

**终端没有mysql命令**

> 找到环境变量设置，在**系统变量**-**Path**变量中添加mysql环境变量指向mysql安装的**bin**目录即可

![image-20220605105636303](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20220605105636303.png)

# redis 

## **设置本地redis可以远程连接**

> 由于本人使用的是**phpstudy**集成开发环境所以这里找到redis目录**D:\phpstudy_pro\Extensions\redis3.0.504**
>
> 文件打开**redis.conf**，然后把**bind 127.0.0.1 改为 bind 0.0.0.0**，最后重启**redis**

```shell
# bind 127.0.0.1 改为 bind 0.0.0.0
bind 0.0.0.0
port 6379
timeout 65
maxclients 10000
databases 16
maxmemory 1048576000
```

## **swoole携程redis代码使用示例**

**连接本地redis示例**

```php
<?php

use Swoole\Coroutine\Redis;
use function Swoole\Coroutine\run;

class RedisTest
{
    public function __construct()
    {

        run(function () {
            $redis = new Redis();
            $redis->connect('172.19.0.1', 6379); # 这里的ip地址修改为本地ip地址不知道的可以终端ipconfig命令查询
            $val = $redis->get('name');
            echo $val . PHP_EOL;
        });
    }
}
new RedisTest();
```

**连接远程redis示例**

```php
<?php

use Swoole\Coroutine\Redis;
use function Swoole\Coroutine\run;

class RedisTest
{
    public function __construct()
    {

        run(function () {
            $redis = new Redis();
            $redis->connect('3.81.36.161', 13822);
            $redis->auth('*********'); # 你的redis服务器密码
            $val = $redis->get('name');
            echo $val . PHP_EOL;
        });
    }
}

new RedisTest();
```

