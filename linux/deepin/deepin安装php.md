​	

```shell
sudo apt update --fix-missing # 更新镜像源
sudo apt -y install php7.4-fpm # 不推荐因为deepin 默认最高php版本是7.3
sudo apt install php #推荐
# 安装默认常用php 扩展
apt -y install  php-mysql php-gd php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring  php-soap curl php-curl
```

- 卸载php

```php
# 删除php的相关包及配置
sudo apt-get autoremove php7*
# 删除关联
    
sudo find /etc -name "*php*" |xargs  rm -rf  
    
# 清除dept列表
 sudo apt purge `dpkg -l | grep php| awk '{print $2}' |tr "\n" " "`
# 检查是否卸载干净（无返回就是卸载完成）
  dpkg -l | grep php7.0
```
