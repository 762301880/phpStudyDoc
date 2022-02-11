# 说明

> 开发时候经常需要swoole环境如果自己搭建可能会把环境搞得很乱用docker的话也需要从头搭建
>
> 以前使用过hyperf 官方提供的swoole-docker开发环境感觉很不错所以拿过来使用一下

#  流程

```shell
# 启动并下载镜像 这里解释一下为什么设置了两个端口  第一个端口用于使用swoole  第二个端口用于访问项目

docker run -v /data/work/:/data/work/ -p 9501:9501 -p 1997:80 --name "laravel_" -itd --entrypoint /bin/sh hyperf/hyperf:7.4-alpine-v3.11-swoole

# 进入容器之后修改镜像源 https://developer.aliyun.com/mirror/alpine?spm=a2c6h.13651102.0.0.3e221b11a7F6xt

cd /etc/apk && sed -i "s/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g" repositories   # 直接用sed命令一键替换
vi /etc/apk/repositories #将里面 dl-cdn.alpinelinux.org 的 改成 mirrors.aliyun.com ; 保存退出即可

# 安装常用软件
apk add vim net-tools

# 安装nginx 卸载 apk del nginx 
apk add nginx

cd /etc/nginx/conf.d 

vim 你的服务器域名||ip.conf      # 容器外部访问容器内部请使用127.0.0.1.conf 可以通过容器外部ip直接

# 如果运行启动 nginx 报错 [emerg] open() "/run/nginx/nginx.pid" failed (2: No such file or directory)
# 参考  https://www.cnblogs.com/chenmingjun/p/10052205.html
bash-5.0# nginx

bash-5.0# [emerg] open() "/run/nginx/nginx.pid" failed (2: No such file or directory)[emerg] open() "/run/nginx/nginx.pid" failed (2: No such file or directory)

bash-5.0# mkdir  /run/nginx/
bash-5.0# chmod -R 777 /run/nginx/
bash-5.0# nginx

# 或者查看日志报错信息
bash-5.0# cat /var/log/nginx/error.log
2021/12/29 00:41:17 [emerg] 36#36: open() "/run/nginx/nginx.pid" failed (2: No such file or directory)
2021/12/29 00:42:55 [emerg] 40#40: unknown directive "erver" in /etc/nginx/conf.d/81.69.231.252.conf:1

# 安装php-fpm
apk add php-fpm
# 启动php-fpm
bash-5.0# find / -name php-fpm*
/etc/logrotate.d/php-fpm7
/etc/init.d/php-fpm7
/etc/php7/php-fpm.d
/etc/php7/php-fpm.conf
/usr/sbin/php-fpm7

bash-5.0# php-fpm7
bash-5.0# netstat -anp | grep 9000
tcp        0      0 127.0.0.1:9000          0.0.0.0:*               LISTEN      174/php-fpm: master 
```

**如果是正常配置项目则创建127.0.0.1.conf** 即可外部访问的时候域名加自己映射的端口号

# 对应nginx 配置

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

