# 一、资料

| name                                         | link-url                                                     |
| -------------------------------------------- | ------------------------------------------------------------ |
| Docker--从入门到实战  利用commit理解镜像构成 | [link](https://yeasy.gitbook.io/docker_practice/image/commit) |
|                                              |                                                              |





## 二、docker 构建容器为镜像

## 2.1 查看正在运行中的镜像

```shell
docker ps -a  # 查看全部运行的容器
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                               NAMES
1a992c1696fa   e66ae809d99a   "docker-php-entrypoi…"   41 minutes ago   Up 41 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp   php
```

## 2.2 提交自己的容器

```shell
docker commit -a "yaoliuyang" -m "打包包含swoole的php"  1a   myphp:7.4.3 
```

## 2.3 查看构建的镜像

```php
REPOSITORY      TAG       IMAGE ID       CREATED          SIZE
myphp           7.4.3     187ca7cbccca   5 seconds ago    494MB
php             7.4.3     e66ae809d99a   16 months ago    405MB # 原来的php镜像   
```

-  打包到`F`盘

```shell
# myphp 需要保存的镜像名称  187 需要保存镜像的镜像id
$ docker save -o   f:\ myphp 187
```



