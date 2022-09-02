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

# compose实战

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

