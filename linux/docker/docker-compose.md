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

![image-20220929083743040](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20220929083743040.png)

### **nginx配置文件default.conf**

> `default.conf` 是 Nginx Web 服务器的配置文件之一，通常用于定义默认的站点配置或全局配置。
>
> `localhost`: 这是一个具体的域名。在这个上下文中，
>
> `localhost` 表示服务器块将匹配请求的域名为 `localhost` 的情况。这通常用于配置本地开发环境，因为 `localhost` 通常用于本地主机。
>
> 这表示当有请求到达服务器的 80 端口时，并且请求的域名是 `localhost` 时，Nginx 将使用这个服务器块中的配置来处理该请求。在这个例子中，处理请求的是 `location /` 中的配置，这是一个简单的示例，实际上可以包含更多配置指令。

```shell
server {
    listen 80;
    server_name localhost;
    root /data/work/laravel_study/public;

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
        fastcgi_pass  app:9000;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
    # 配置websocketnginx转发连结
    location /ws {
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $host;
            # 这里的端口号与你的Swoole WebSocket服务器绑定的端口一致
            #proxy_pass http://127.0.0.1:9501;

            # 因为我们这里使用的是容器配置所以用启动容器的名称创立转发
            proxy_pass http://app:9501;
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
php-fpm & 2>/dev/null
echo -e "php-fpm启动成功...\n"

echo -e "======================启动定时任务========================\n"
service cron start &  2>/dev/null
echo -e "定时任务启动成功...\n"

echo -e "======================启动守护进程========================\n"
service supervisor start & 2>/dev/null
supervisorctl restart all
echo -e "启动守护进程启动成功...\n"

# 等待程序运行
wait
#/bin/bash



```

### **Dockerfile**

```shell
# 设置基础镜像
FROM php:7.4-fpm

# 拷贝镜像源
COPY  ./debian/debian11_source_list /etc/apt/
# 拷贝php配置
COPY  ./php/php_ext/ /usr/local/etc/php
#拷贝swoole软件
COPY ./php/swoole-4.8.1.tgz /usr/local
COPY ./ssh/start_service.sh /bin/
# 拷贝compose配置文件&安装依赖包composer&修改镜像源
COPY ./php/composer.phar /usr/local/bin/composer
COPY ./supervisor/ /etc/supervisor/conf.d

RUN cp /etc/apt/sources.list /etc/apt/sources.list.back \
    && chmod -R 777 /etc/apt/debian11_source_list \
    && cat  /etc/apt/debian11_source_list > /etc/apt/sources.list \
    && apt-get update \
    # zlib1g-dev libpng-dev libjpeg-dev 一定要先后安装为了安装gd库
    && apt-get install -y git zip unzip vim wget net-tools cron zlib1g-dev libpng-dev libjpeg-dev libfreetype6-dev dos2unix supervisor \
    # 安装swoole
    && pecl install /usr/local/swoole-4.8.1.tgz \
    && cp /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini \
    && cat /usr/local/etc/php/php_ext >>  /usr/local/etc/php/php.ini \
    # 安装php扩展
    && docker-php-ext-configure  gd --with-jpeg --with-freetype \
    # sockets 用于rabbitmq所需
    && docker-php-ext-install  mysqli pdo_mysql gd  exif  sockets\
    # 安装redis扩展
    && pecl install --alldeps redis \
    # 开启redis扩展
    && docker-php-ext-enable redis  \
    # 添加compose
    && chmod -R 777 /usr/local/bin/composer \
    && composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/ \
    # 设置时区
    && cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    # 添加定时任务
    && touch ~/crontab \
    && chmod 600 ~/crontab \
    && echo "PATH=/usr/local/bin/php:/usr/local/bin:/usr/bin:/bin" >> ~/crontab \
    && echo "* * * * * cd /data/work/laravel_study && /usr/local/bin/php artisan schedule:run >> /dev/null 2>&1" >> ~/crontab \
    && mv ~/crontab /var/spool/cron/crontabs/root \
    # 配置守护进程日志
    && mkdir /etc/supervisor/log && chmod -R 777 /etc/supervisor/log \
    # 配置启动文件
    && chmod a+x  /bin/start_service.sh \
    && dos2unix  /bin/start_service.sh





# 设置工作目录
WORKDIR /data/work

# 设置容器对外暴露的端口号
EXPOSE 9000


# 配置启动命令
CMD ["/bin/start_service.sh"]
```

### **docker-compose.yml**

```shell
version: '3'

services:
  app:
    build:
      context: ./docker
      dockerfile: Dockerfile
    container_name: php7.4-fpm
    restart: always
    privileged: true
    tty: true
    volumes:
      - .:/data/work/laravel_study/
    networks:
      - web
    ports:
      - 9501:9501

  web:
    container_name: nginx
    image: nginx:latest
    restart: always
    ports:
      - 1997:80
    volumes:
      - ./docker/nginx/default.conf:/etc/nginx/conf.d/default.conf
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
      MYSQL_DATABASE: ${DB_DATABASE}
      MYSQL_USER: ${DB_USERNAME}
      MYSQL_PASSWORD: ${DB_PASSWORD}
      MYSQL_ROOT_PASSWORD: ${DB_ROOT_PASSWORD}
    ports:
      - "3306:3306"
    networks:
      - web
  redis:
    image: redis
    container_name: redis
    ports:
      - "6379:6379"
    volumes:
      - /data/redis_data:/data
    networks:
      - web
#    environment:
#      - REDIS_PASSWORD=your_redis_password  # 设置Redis密码
networks:
  web:
    driver: bridge
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

## dockerfile如何同时启动多个程序

要在Docker容器中同时启动多个程序，你可以通过创建一个启动脚本或使用supervisor来管理进程。下面是两种常见的方法：

1. 创建启动脚本：
   在Dockerfile中添加以下命令：

   ```docker
   # 复制启动脚本到容器中
   COPY start.sh /start.sh
   # 设置启动脚本可执行权限
   RUN chmod +x /start.sh
   # 执行启动脚本
   CMD ["./start.sh"]
   ```

   然后，创建一个名为 `start.sh` 的启动脚本，内容如下：

   ```bash
   #!/bin/bash
   
   # 启动程序1
   /path/to/program1 &
   
   # 启动程序2
   /path/to/program2 &
   
   # 等待程序运行
   wait
   ```

   在启动脚本中，你可以依次启动多个程序，并使用 `&` 操作符将其放入后台运行。`wait` 命令将等待所有程序运行结束。

2. 使用supervisor管理进程：
   安装supervisor，并创建一个配置文件（例如 `program.conf`）来定义要同时运行的程序。在Dockerfile中添加以下命令：

   ```docker
   # 安装supervisor
   RUN apt-get update && apt-get install -y supervisor
   
   # 复制supervisor配置文件到容器中
   COPY program.conf /etc/supervisor/conf.d/program.conf
   
   # 启动supervisor
   CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]
   ```

   在 `program.conf` 文件中，你可以定义每个程序的配置。例如：

   ```conf
   [program:program1]
   command=/path/to/program1
   
   [program:program2]
   command=/path/to/program2
   ```

   当容器启动时，supervisor将负责启动并管理这些程序。

无论你选择哪种方式，都可以实现在Docker容器中同时启动多个程序。根据你的需求和偏好，选择最适合你的方法即可。



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

## **配置定时任务无法执行**

> linux cron定时任务 * * * * * echo 222222 >> aaa.txt 这种可以执行 * * * * * echo $(date '+%Y-%m-%d %X') >> ~/test_cron.txt 这种不能执行

```shell
您好！根据您提供的信息，似乎第一个命令可以正常执行，但第二个命令无法执行。

根据您提供的第二个命令：`* * * * * echo $(date '+%Y-%m-%d %X') >> ~/test_cron.txt`，这是一个将当前时间追加到`test_cron.txt`文件中的命令。

在Cron任务中，命令行环境可能与您在终端中使用的不同。要确保在Cron执行的命令中使用正确的环境变量和路径，可以尝试以下方法之一来解决问题：

1. 完整指定命令的绝对路径：您可以使用命令`which`来查找命令在系统中的完整路径，例如：`/usr/bin/date`。将命令替换为完整路径后，Cron就可以正确执行它。
2. 在Cron任务中设置环境变量：如果在命令行中使用了自定义环境变量，请确保在Cron任务中设置相同的环境变量。您可以使用Shell脚本或特殊的Cron环境文件来设置这些变量，例如在Cron任务之前添加`.bashrc`文件的引用：`. /home/your_username/.bashrc`。

请注意，Cron任务执行时没有终端环境，所以要确保命令在没有用户交互的情况下能够正确执行。

希望这些提示能帮助您解决问题。如果还有其他疑问，请随时提问！
```

**为什么会出现这种情况**

出现这种情况的原因可能是以下几个方面：

1. 环境变量：Cron任务是在系统后台运行的，它可能没有与您的用户环境相同的环境变量。因此，如果您在命令中使用了自定义环境变量，那么在Cron任务中就无法识别和使用它们。您需要在Cron任务中显式设置或引用这些环境变量。
2. 路径问题：在命令行中使用的某些命令的完整路径可能与Cron任务中的不同。Cron任务没有终端交互，所以它不会根据您的$PATH环境变量来查找命令。要解决这个问题，您可以在Cron任务中使用完整的命令路径，如`/usr/bin/date`。
3. 文件路径权限问题：Cron任务在执行时使用的用户可能没有对特定文件或目录的写权限。请确保Cron任务所使用的用户对目标文件的路径有适当的访问权限。

总的来说，Cron任务是在不同的环境中运行的，与交互式终端会话有所不同。因此，您需要特别关注环境变量、命令路径和文件权限等问题，以确保Cron任务能够正确执行。

怎么能让cron使用当前用户环境相同的环境变量

要让Cron任务使用当前用户环境相同的环境变量，可以按照以下步骤操作：

1. 打开您的Cron配置文件：在终端中使用以下命令打开Cron配置文件：

   ```
   crontab -e
   ```

   

2. 在配置文件中设置环境变量：在打开的配置文件中添加您所需的环境变量。例如，如果您想要设置`PATH`环境变量，可以在文件开头添加类似如下的行：

   > 在**crontab -e**中添加

   ```shell
   PATH=/your/custom/path:/usr/local/bin:/usr/bin:/bin
   
   # /your/custom/path 是指你环境变量的位置
   # 例如修改为
   PATH=/usr/local/bin/php:/usr/local/bin:/usr/bin:/bin
   ```

   >
   > `/your/custom/path` 只是一个示例路径，您应该将其替换为实际的自定义路径。
   >
   > 在Linux系统中，`PATH` 是一个环境变量，用于指定可执行程序的搜索路径。当您在终端中运行命令时，系统会在 `PATH` 指定的路径中查找这些命令的可执行文件。
   >
   > 例如，如果您将 `PATH` 设置为 `/usr/local/bin:/usr/bin:/bin`，则系统会首先在 `/usr/local/bin` 中查找可执行文件，然后在 `/usr/bin` 中查找，最后在 `/bin` 中查找。如果所需的可执行文件在这些路径中的任何一个中存在，系统就能够找到并执行它。
   >
   > 回到您的问题，如果您想在 Cron 任务中使用自定义路径，您应该将 `/your/custom/path` 替换为实际的路径，例如 `/home/your_username/custom/bin:/usr/local/bin`。这将告诉 Cron 在这些路径中查找命令的可执行文件。
   >
   > 请确保将 `/your/custom/path` 替换为您想要使用的实际自定义路径。
   >
   > 深感歉意对于之前的回答中的错误解释。如果您还有其他问题，请随时提出。
   >
   > 
   >
   > `/your/custom/path` 只是一个示例路径，表示用户可以将其替换为实际的自定义路径。
   >
   > 例如，如果您有一个自定义的脚本，位于您的用户目录下的 `scripts` 文件夹中，您可以将 `/your/custom/path` 替换为实际路径，如 `/home/your_username/scripts`。这样，Cron 任务就可以访问并执行此路径中的脚本。
   >
   > 换句话说，`/your/custom/path` 是一个占位符，用于表示您可以将其替换为特定的文件夹路径，以指定要在 Cron 任务中使用的自定义路径。
   >
   > 请根据您的实际需求将 `/your/custom/path` 替换为适当的路径，以确保 Cron 任务可以访问所需的文件或脚本。
   >
   > 再次对之前的回答中的错误给予道歉。如有任何其他问题，请随时提问。

   

   请按照您自己的需求设置其他环境变量。

3. 保存并退出文件：完成环境变量的设置后，保存并退出Cron配置文件。

4. 重启Cron服务：使更改生效，需要重新启动Cron服务。在终端中，使用以下命令重启Cron服务：

   ```
   sudo service cron restart
   ```


现在，Cron任务将在执行时使用与当前用户相同的环境变量。请注意，这样做可能会增加Cron任务的复杂性和依赖性，因为它依赖于当前用户的环境设置。在设置环境变量时，请确保遵循正确的语法和设置安全性。

希望这能帮到您！如果您还有其他疑问，请随时提问。

### **<font color='red'>如果配置之后依然无法执行</font>**

**可能性排查**

> 1  排查写入的**root** crontab 配置文件执行权限 我的是**600**  本人排查是这个原因  直接给出 **chmod 600 root** 即可