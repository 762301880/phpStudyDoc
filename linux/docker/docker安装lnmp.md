#  一、docker 安装php

- 参考资料

| name                     | link_url                                                     |
| ------------------------ | ------------------------------------------------------------ |
| 菜鸟教程-Docker 安装 PHP | [link](https://www.runoob.com/docker/docker-install-php.html) |
|                          |                                                              |



##  1.1  下载php镜像

```shell
docker pull php:7.4-fpm
```

## 1.2 启动php容器

```shell
docker run -itd  -p 80:80 --name php  容器id
```

### 1.2.1 进入容器内部

- 查看正在运行中的容器

```shell
docker ps -a
# 显示如下
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS          PORTS                               NAMES
9afd02d24578   php:7.4.3   "docker-php-entrypoi…"   23 seconds ago   Up 21 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp   php
```

- 进入容器内部

```shell
docker exec -it 容器id /bin/bash
```

- 配置php.ini

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
# 3.退出容器
exit
```

# 二、docker安装nginx

## 2.1 拉去php镜像

```shell
docker pull nginx 
```

- 查看拉取的镜像

```shell
docker images
```

## 2.2  启动镜像

```shell
# linux
docker run -itd  --name mynginx  -p 8080:80  -v /etc/nginx:/www  容器id
# windows 
docker run -itd  --name mynginx  -p 8080:80  -v c:/etc/nginx:/www  容器id
```

## 2.3 nginx 容器配置文件位置

- nginx的配置目录

```shell
/etc/nginx
```

- nginx 多主机配置目录

```shell
/etc/nginx/conf.d/
# 在这里配置的默认启动文件位置需要重启nginx 才能使配置生效
nginx -s reload
# 或则
service nginx reload
```



## 2.4 nginx结合php搭建网站

- 参考[资料](https://blog.csdn.net/gaoxuaiguoyi/article/details/106818303)

### 2.4.1 启动nginx

```shell
docker pull nginx # 下载nginx
# 启动nginx
# 第一个是挂载是挂载配置文件  第二个挂载是挂载项目存放目录
docker run --name mynginx -itd -p 8080:80 -v C:\etc\nginx\conf.d:/etc/nginx/conf.d/ -v C:\etc\nginx\www:/www  容器id
```

### 2.4.2 安装php

```shell
# 下载php
docker pull php:7.4-fpm
# 启动php -v 将本地项目目录挂载到容器 内部的www目录
docker run docker run --name  myphp -v C:\etc\nginx\www:/www  -itd  容器id
```

### 2.4.5 最后的配置

```shell
# 在本地修改nginx配置文件
cp default.conf default.conf.cp # 备份一份配置文件 
vim default.conf #编辑默认的配置文件


server {
    listen       80;
    listen  [::]:80;
    server_name  localhost;
    #access_log  /var/log/nginx/host.access.log  main;
    location / {
        root   /www/;    # 此处修改为nginx中挂载的项目目录
        index index.php index.html index.htm;
    }
    #error_page  404              /404.html;
    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}
    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
        root           html;
        fastcgi_pass   172.17.0.3:9000; # 此处修改为php的网络,使用 docker inspect 容器id命令 IPAddress 字段就是对应的网络地址
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /www/$fastcgi_script_name; # /www/ 修改为 php内部挂载的项目目录
        include        fastcgi_params;
    }
    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}


```

