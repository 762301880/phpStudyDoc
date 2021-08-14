# 说明

> Nginx部署ThinkPHP项目URL模式不生效的解决方法

> 由于本人使用的是linux开发,在使用nginx搭建了thinkphp的服务发现访问一直是报的根
>
> 目录下面的信息,如何切换路由都没有用,查阅百度得到了解决方案

参考[资料](https://www.cnblogs.com/jianzhaojing/articles/11336544.html)



# nginx配置

- 本人nginx-thinkphp配置

```php
server {
    listen 80;
    server_name www.think.com; # 切换为你自己的虚拟域名
    root /home/yaoliuyang/公共的/think/thinkphp_study/public/; #切换为你自己的项目目录

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.php;

    charset utf-8;

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;
    # 解决访问全市根目录的方法
    #访问路径的文件不存在则重写URL转交给ThinkPHP处理
    location / {
    if (!-e $request_filename){
        rewrite ^/(.*)$ /index.php?s=/$1 last;
       }
    }
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

