## 参考

| 微软 | [link](https://learn.microsoft.com/zh-cn/dotnet/architecture/microservices/multi-container-microservice-net-applications/multi-container-applications-docker-compose) |
| ---- | ------------------------------------------------------------ |
|      |                                                              |



# 简介

> docker
>
> DockerFile build run 手动操作，单个容器
>
> 微服务,100个微服务! 依赖关系
>
> Docker Compose 来轻松高效的管理容器。定义和运行多个容器。
>
> 使用 Compose 基本上是一个三步过程：
>
> 1. 定义您的应用程序的环境，`Dockerfile`以便可以在任何地方复制它。
> 2. 定义构成您的应用程序的服务，`docker-compose.yml` 以便它们可以在隔离环境中一起运行。
> 3. 运行`docker compose up`，[Docker compose 命令](https://docs.docker.com/compose/#compose-v2-and-the-new-docker-compose-command)启动并运行您的整个应用程序。您也可以`docker-compose up`使用 Compose Standalone（`docker-compose`二进制）运行。
>
> 作用:批量容器编排
>
> Compose是Docker官方的开源项目。需要安装!
>
> `Dockerfile`让程序在任何地方运行。web服务。reids,mysql,nginx,多个容器

## **资料**

| 名称           | 地址                                     |
| -------------- | ---------------------------------------- |
| docker-compose | [link](https://docs.docker.com/compose/) |



# [docker-compose安装](https://www.runoob.com/docker/docker-compose.html)

**资料**

| 名称                  | 地址                                                         |
| --------------------- | ------------------------------------------------------------ |
| docker官网compose安装 | [link ](https://docs.docker.com/compose/install/)  [国内镜像安装](https://get.daocloud.io/) |
| 第三方博客            | [link](https://yeasy.gitbook.io/docker_practice/compose/install) |

## **linux安装**

```shell
#----------------------------------不建议试用国外镜像----------------------------------------
# 运行以下命令以下载 Docker Compose 的当前稳定版本

sudo curl -L "https://github.com/docker/compose/releases/download/v2.2.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# 创建可执行权限

sudo chmod +x /usr/local/bin/docker-compose

# 创建软链

sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

# 测试是否安装成功：

docker-compose --version

#----------------------------------用这个国内镜像----------------------------------------

# 以上下载很慢可以用国内的镜像(你可以通过修改URL中的版本，可以自定义您的需要的版本。)

curl -L https://get.daocloud.io/docker/compose/releases/download/v2.2.3/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

# compose实战(网络参考)

> 简单来说**composer**就是启动你写好的执行的生成的**dockerfile**镜像，不需要再去手动的**run**启动命令
>
> **docker-compose up 100个服务**
>
> composer:重要的概念
>
> - 服务service,容器,应用。(web,redis,mysql...)
> - 最终生成出来的就叫做**project** 项目(一组关联的容器)

**菜鸟教程案例**

```shell
# yaml 配置实例
version: '3'
services:
  web:
    build: .
    ports:
   - "5000:5000"
    volumes:
   - .:/code
    - logvolume01:/var/log
    links:
   - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

[**官方docker-compose案例**](https://docs.docker.com/compose/gettingstarted/)

> 1. 应用 app.py
>
> 2. Dockerfile应用打包为镜像(单机玩具)
>
> 3. docker-compose.yaml文件(定义整个服务,需要的环境,web,redis...)   完整的上线服务
>
> 4. 启动compose项目(docker-compose up)
>
> 流程:
>
> 1. 创建网络
> 2. 执行docker-compose.yaml
> 3. 启动服务
>
> Docker-compose yaml
>
> Creating composetest_web_1 ..done
>
> Creating composetest_redis_1 ..done

1. 文件名 composetest
2. 服务

```shell
version: '3'
services:
  web:
    build: .
    ports:
     - "5000:5000"
  redis:
    image: "redis:alpine"
```

自动创建默认规则





# 个人**docker-compose实战**

## 搭建laravel项目

**资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://www.iteye.com/blog/laomn-2440511) [link](https://blog.csdn.net/qq_41980563/article/details/88880719) [link](https://www.it1352.com/2725087.html) [link](https://www.cnblogs.com/panbin2006/p/16529040.html) [link](https://huaweicloud.csdn.net/63311b68d3efff3090b52482.html?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Eactivity-1-119813498-blog-125740113.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Eactivity-1-119813498-blog-125740113.pc_relevant_default&utm_relevant_index=2) [link](https://stackoverflow.com/questions/66638731/why-do-i-need-tty-true-in-docker-compose-yml-and-other-images-do-not) [link](https://cloud.tencent.com/developer/ask/sof/111359) [link](https://learnku.com/articles/43525) [link](https://yeasy.gitbook.io/docker_practice/compose/compose_file) [link](https://docs.docker.com/compose/compose-file/compose-file-v3/) |
|          |                                                              |



> 如下图所示首先我们定义了一个docker目录下存放docker相关配置，根目录下新建了**Dockerfile&docker-compose.yml**配置文件

![image-20220929083743040](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220929083743040.png)

### **nginx配置文件81.69.231.252.conf**

> 目录名对应服务器的ip地址 如果是本地修改为**127.0.0.1.conf**
>
> 对应监听地址也要修改**server_name**

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

### **crontab 文件**

>  里面配置对应的项目的定时任务

```shell
* * * * * cd /data/work/laravel_study && php artisan schedule:run >> /dev/null 2>&1
```

### **start_service.sh**

> 启动项目所需脚本

```shell
#!/bin/sh


echo -e "======================启动php-fpm========================\n"
php-fpm7 2>/dev/null
echo -e "php-fpm启动成功...\n"


echo -e "======================启动nginx========================\n"
nginx  2>/dev/null
echo -e "nginx启动成功...\n"

echo -e "======================启动定时任务========================\n"
crond & 2>/dev/null
echo -e "定时任务启动成功...\n"

/bin/bash

```

### **Dockerfile**

```shell
FROM  hyperf/hyperf:7.4-alpine-v3.11-swoole
# 设置项目的路径
ENV  PROJECT_PATH  /data/work/laravel_study/
# 设置日志存储目录名称
ENV  LOG_DIRECTORY_NAME storage
# 设置日志目录全路径
ENV  PROJECT_LOG_PATH $PROJECT_PATH$LOG_DIRECTORY_NAME
# 指定工作目录(进入启动的容器终端会自动进入工作目录)
WORKDIR $PROJECT_PATH
# 修改镜像源
RUN  cd /etc/apk && sed -i "s/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g" repositories \
    && composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/ \
    # 设置修改容器内部时区
    && cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
# 拷贝当前项目到容器工作目录
COPY $PWD  $PROJECT_PATH
# 设置定时任务脚本
COPY ./docker/crontab /var/spool/cron/crontabs/root
# 安装所需软件
RUN apk add vim net-tools \
     && apk add dos2unix \
     && apk add nginx \
     && mkdir -p  /run/nginx/ && chmod -R 777 /run/nginx/ \
     && apk add php-fpm \
     && cd $PROJECT_PATH && chmod -R 777 $PROJECT_LOG_PATH  &&  composer install
# 将nginx 配置文件拷贝到容器中
COPY  ./docker/81.69.231.252.conf   /etc/nginx/conf.d/
COPY  ./docker/start_service.sh   /bin/
RUN  chmod a+x /bin/start_service.sh
RUN  dos2unix /bin/start_service.sh
# 暴露端口号(指的是暴露这些端口 -P 命令可以自动生成暴露的端口)
EXPOSE 9501 80
CMD  ["/bin/start_service.sh"]
```

### **docker-compose.yml**

```shell
# yaml 配置
version: '3'
services:
  web:
    container_name: laravel_study
    build: .
    ports:
      - "1997:80"
    tty: true
    restart: always # 默认重启
#    command:
#      - /bin/bash
#      - -c
#      - |
#        php-fpm7 2>/dev/null
#        nginx  2>/dev/null
#        /bin/bash

    
   # command: ["/bin/start_service.sh"]

```

# Dockerfile 扩展补充

## Dockerfile写入linux定时任务动态

**资料**

| 名称 | 地址                                                         |
| ---- | ------------------------------------------------------------ |
| 博客 | [link](https://www.cnblogs.com/HYanqing/p/12213185.html)  [link](https://www.likecs.com/show-205295903.html) |



> 上面所示的写入定时任务不是那么友好，需要手动编辑一个定时任务脚本文件这里我们扩展一下

```dockerfile
FROM  hyperf/hyperf:7.4-alpine-v3.11-swoole
# 设置项目的路径
ENV  PROJECT_PATH  /data/work/laravel_study/
# 设置日志存储目录名称
ENV  LOG_DIRECTORY_NAME storage
# 设置日志目录全路径
ENV  PROJECT_LOG_PATH $PROJECT_PATH$LOG_DIRECTORY_NAME
# 指定工作目录(进入启动的容器终端会自动进入工作目录)
WORKDIR $PROJECT_PATH
# 修改镜像源
RUN  cd /etc/apk && sed -i "s/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g" repositories \
    && composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/ \
    # 设置修改容器内部时区
    && cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    # 创建一个定时脚本文件
    && touch ~/crontab \
    # 写入定时脚本
    && echo "* * * * * cd ${PROJECT_PATH} && php artisan schedule:run >> /dev/null 2>&1" >> ~/crontab \
    # 写入定时脚本
    && mv ~/crontab /var/spool/cron/crontabs/root

# 拷贝当前项目到容器工作目录
COPY $PWD  $PROJECT_PATH
## 设置定时任务脚本
#COPY ./docker/crontab /var/spool/cron/crontabs/root
# 安装所需软件
RUN apk add vim net-tools \
     && apk add dos2unix \
     && apk add nginx \
     && mkdir -p  /run/nginx/ && chmod -R 777 /run/nginx/ \
     && apk add php-fpm \
     && cd $PROJECT_PATH && chmod -R 777 $PROJECT_LOG_PATH  &&  composer install
# 将nginx 配置文件拷贝到容器中
COPY  ./docker/127.0.0.1.conf  /etc/nginx/conf.d/
COPY  ./docker/start_service.sh   /bin/
RUN  chmod a+x /bin/start_service.sh
RUN  dos2unix /bin/start_service.sh
# 暴露端口号(指的是暴露这些端口 -P 命令可以自动生成暴露的端口)
EXPOSE 9501 80

CMD  ["/bin/start_service.sh"]
```

## docker-compose up -d 启动后自动删除垃圾容器

> 不知道为什么构建容器之后目前会生成几个垃圾容器还没找到具体原因

### docker system prune 删除

`docker system prune` 是 Docker 提供的一个命令，可以一次性删除所有无用的 Docker 资源，包括：

- 没有被使用的容器；
- 没有被使用的镜像；
- 没有被使用的匿名和挂起的数据卷；
- 没有被使用的网络。

使用 `docker system prune` 命令可以清理掉不需要的 Docker 资源，释放计算机的磁盘空间和系统资源，并且避免产生垃圾容器和镜像。

`docker system prune` 命令的使用非常简单，只需要在 Docker 主机中执行即可：

```
docker system prune
```



在执行该命令后，Docker 会列出要删除的所有无用资源，并提示你确认删除。如果确认删除，请输入 `Y` 或 `yes`，然后按 Enter 键。删除无用资源可能需要花费一段时间，具体耗时取决于要删除的资源数量和大小。

如果你想自动删除所有无用资源，而不需进行确认，请使用以下命令：

```
docker system prune -f
```



总之，`docker system prune` 命令是一种清理无用 Docker 资源的有效方法，可以使 Docker 主机保持整洁，节省磁盘空间，避免垃圾容器和镜像的产生。建议定期运行该命令，尤其是在 Docker 主机空间不足或需要释放系统资源时

**结合使用**

```shell
docker-compose up -d && docker system prune -f
```

### 删除所有启动的容器以及数据卷&镜像

> 这将会删除所有已经停止的容器，删除所有构建的镜像，删除所有卷，并删除所有孤儿容器。之后你可以重新运行 `docker-compose up -d` 命令重新构建和启动所有服务。

```shell
docker-compose down --rmi all --volumes --remove-orphans
```



# 遇到的bug解析

## docker-compose 修改mysql配置没有生效(密码)

**我的配置文件**

> **解决方案**
>
> 你可以尝试以下步骤：
>
> 1. 在执行 `docker-compose up` 启动服务之前，将 `./docker/db_data` 目录删除。
> 2. 修改 `docker-compose.yml` 文件中的 `MYSQL_ROOT_PASSWORD` 变量为你想要的新密码。
> 3. 重新执行 `docker-compose up -d` 启动服务。
>
> 这样可以确保 Docker 重新创建一个带有新密码的 MySQL 数据库容器。如果你仍然不行，可以执行以下步骤：
>
> 1. 停止并删除所有的 Docker 容器和网络： `docker-compose down --volumes`
> 2. 删除 `./docker/db_data` 目录。
> 3. 修改 `docker-compose.yml` 文件中的 `MYSQL_ROOT_PASSWORD` 变量为你想要的新密码。
> 4. 重新执行 `docker-compose up -d` 启动服务。
>
> 这样应该能够让新密码生效。

```php
version: '3'

services:
  app:
    build: docker
    container_name: php7.4-fpm
    restart: always
    volumes:
      - .:/data/work/laravel_study/
    networks:
      - web

  web:
    container_name: nginx
    image: nginx:latest
    restart: always
    ports:
      - 1997:80
      - 9501:9501
    volumes:
      - ./docker/nginx/nginx.conf:/etc/nginx/conf.d/60.204.148.255.conf
      - .:/data/work/laravel_study/
    depends_on:
      - app
      - db
    networks:
      - web
  db:
    container_name: mysql-8.0
    image: mysql:8.0
    volumes:
      - /data/db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_DATABASE: laravel
      MYSQL_USER: laravel
      MYSQL_PASSWORD: laravel
      MYSQL_ROOT_PASSWORD: 123456
    ports:
      - "3306:3306"
    networks:
      - web
networks:
  web:
    driver: bridge

```

**<font color='red'>需要将对应的数据卷删除在重写执行 docker-compose up -d</font>**
