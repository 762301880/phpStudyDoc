- docker-php 安装swoole

  `安装swoole`

  ```shell
  pecl install swoole
  ```

  配置swoole

```shell
# 1. 进入容器内部之后查询配置文件的地址
root@9afd02d24578:/usr/local/etc/php# find / -name php
/usr/local/etc/php # 可以看出这个就是配置文件地址的目录
/usr/local/lib/php
/usr/local/lib/php/doc/swoole/examples/php
/usr/local/bin/php
/usr/local/include/php
/usr/local/php
# 2. 进入配置文件目录
cd /usr/local/etc/php
ls
conf.d   php.ini-development  php.ini-production
cp php.ini-development php.ini # 将开发配置复制一份
# 打开php配置文件中配置
vim cp php.ini
extension=swoole.so # 找任意一行在其中加入
```

`查看swoole是否安装成功`

```php
php -m
[PHP Modules]
Core
ctype
curl
date
dom
fileinfo
filter
ftp
hash
iconv
json
libxml
mbstring
mysqlnd
openssl
pcre
PDO
pdo_sqlite
Phar
posix
readline
Reflection
session
SimpleXML
sodium
SPL
sqlite3
standard
swoole # 安装成功便会展示
tokenizer
xml
xmlreader
xmlwriter
zlib

[Zend Modules]
```

