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
    root /data/work/laravel_study/public; # 指向laravel 框架的public 目录

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

**补充**

```shell
# 如果玩laravel 项目的时候提示缺少phpinfo扩展
composer update --ignore-platform-req=ext-fileinfo
```

**推荐直接使用第三方的docker镜像例如hyperf的开发镜像**

```shell
# 启动并下载镜像
docker run -v /data/work/laravel_study/:/data/work/laravel_study  -p 1997:80 -itd --entrypoint /bin/sh hyperf/hyperf:7.4-alpine-v3.11-swoole
# 进入容器之后修改镜像源 https://developer.aliyun.com/mirror/alpine?spm=a2c6h.13651102.0.0.3e221b11a7F6xt
vi /etc/apk/repositories #将里面 dl-cdn.alpinelinux.org 的 改成 mirrors.aliyun.com ; 保存退出即可
# 安装常用软件
apk add vim 
apk add net-tools

# 安装nginx 卸载 apk del nginx 
apk add nginx

cd /etc/nginx/conf.d 

vim 你的服务器域名||ip.conf

# 如果运行启动 nginx 报错 [emerg] open() "/run/nginx/nginx.pid" failed (2: No such file or directory)
bash-5.0# nginx

bash-5.0# [emerg] open() "/run/nginx/nginx.pid" failed (2: No such file or directory)[emerg] open() "/run/nginx/nginx.pid" failed (2: No such file or directory)

bash-5.0# mkdir  /run/nginx/
bash-5.0# chmod -R 777 /run/nginx/

# 或者查看日志报错信息
bash-5.0# cat /var/log/nginx/error.log
2021/12/29 00:41:17 [emerg] 36#36: open() "/run/nginx/nginx.pid" failed (2: No such file or directory)
2021/12/29 00:42:55 [emerg] 40#40: unknown directive "erver" in /etc/nginx/conf.d/81.69.231.252.conf:1


```

