#  一、docker 安装php

- 参考资料

| name                     | link_url                                                     |
| ------------------------ | ------------------------------------------------------------ |
| 菜鸟教程-Docker 安装 PHP | [link](https://www.runoob.com/docker/docker-install-php.html) |
|                          |                                                              |



##  1.1  下载php镜像

```shell
docker pull php:7.4.3
```

## 1.2 启动php容器

```shell
docker run -itd  -p 80:80 --name php  php:7.4.3
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
docker exec -it +CONTAINER_ID  /bin/bash
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
docker run -itd  --name mynginx  -p 8080:80  -v /etc/nginx:/www +r
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
```



