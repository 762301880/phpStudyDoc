# 资料

| 名称 | 地址                                            |
| ---- | ----------------------------------------------- |
| 官网 | [link](https://www.fastadmin.net/download.html) |

# 环境搭建

##  linux_nginx配置

**添加虚拟域名**

> **/etc/hosts** 中添加

```shell
# 自定义
127.0.0.1  www.fastadmin.com
```

**添加nginx配置文件**

> 在**/etc/nginx/sites-enabled**目录下添加**www.fastadmin.com**

```shell

yly@yly-pc:/etc/nginx/sites-enabled$ cat www.fastadmin.com 
server {
    listen 80;
    server_name www.fastadmin.com;
    root /home/yly/Documents/work/fastadmin/public/;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.php;

    charset utf-8;

   location / {
    if (!-e $request_filename){
        rewrite ^/(.*)$ /index.php?s=/$1 last;
       }
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

**bug解析**

遇到nginx错误问题 请查看** cat /var/log/nginx/error.log**

一般都是目录权限问题

## 安装

> 域名/install.php执行安装

```php
www.fastadmin.com/install.php
```

