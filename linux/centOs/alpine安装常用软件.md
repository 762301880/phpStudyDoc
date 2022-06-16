#  php扩展

##  安装php-mysqli扩展

```php
# 安装php-mysqli扩展
/**
 * 查询扩展
 */
bash-5.0# apk search mysqli  
php7-mysqli-7.4.26-r1
mariadb-client-10.4.22-r0
/**
 * 安装扩展
 */    
bash-5.0# apk add php7-mysqli
(1/1) Installing php7-mysqli (7.4.26-r1)
OK: 95 MiB in 86 packages    
 /**
 * 查询安装
 */   
 bash-5.0# php -m
[PHP Modules]
.....
mysqli
.....
```

