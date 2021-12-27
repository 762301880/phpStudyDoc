[^createdat:2021/12/27]

[^author:姚留洋]

# 说明

> 恰巧买了一个服务器一年，想着为了不把本地的环境弄乱全面采用docker配置开发测试环境算了

## [参考资料](https://www.cnblogs.com/jfaith/p/11991228.html)

# 配置

> 如果有域名并解析的话请修改为自己的域名
>
> 加入你的服务器公网ip是**81.69.231.252** 在 **/etc/nginx/conf.d**下建立配置文件**81.69.231.252.conf**
>
> 在**/usr/share/nginx/html**目录下建立index.php用于测试访问即可

```shell
server {
    listen 80;
    server_name 81.69.231.252;
    root /usr/share/nginx/html;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.php;
    client_max_body_size 100m;
    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        # fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_pass  127.0.0.1:9000;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}

```

