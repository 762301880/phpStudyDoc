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

