# 常用命令

## 帮助命令

```shell
docker --version           # 显示docker的版本信息
docker info                # 显示docker的系统信息，包括镜像和容器的数量
docker 命令  --help         # 帮助命令
```

## 帮助文档的[地址](https://docs.docker.com/engine/reference/builder/)

## 镜像命令

### docker images       查看docker中所有的镜像

```shell
[root@VM-24-20-centos ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/nginx     latest              4cdc5dd7eaad        3 days ago          133 MB
# 解释
REPOSITORY 镜像的仓库源
TAG        镜像的标签
IMAGE ID   镜像的id
CREATED    镜像的创建时间
SIZE       镜像的大小
# 可选项
Options:
  -a, --all             #显示全部的镜像
      --digests         Show digests
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print images using a Go template
      --help            Print usage
      --no-trunc        Don't truncate output
  -q, --quiet           # 只显示镜像的id
```

### docker search 搜索镜像

```shell
[root@VM-24-20-centos ~]# docker search mysql
INDEX       NAME                                        DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
docker.io   docker.io/mysql                             MySQL is a widely used, open-source relati...   11108     [OK]       
docker.io   docker.io/mariadb                           MariaDB Server is a high performing open s...   4211      [OK]       
docker.io   docker.io/mysql/mysql-server                Optimized MySQL Server Docker images. Crea...   824                
# 可选项，通过收藏过滤
--filter=STARS=3000 #搜索出来的镜像就是STARS大于3000的
[root@VM-24-20-centos ~]# docker search mysql --filter=STARS=3000
INDEX       NAME                DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
docker.io   docker.io/mysql     MySQL is a widely used, open-source relati...   11108     [OK]       
docker.io   docker.io/mariadb   MariaDB Server is a high performing open s...   4211      [OK] 
```

### docker pull 下载镜像

```shell
# 下载镜像 docker pull 镜像名:版本号tag

[root@VM-24-20-centos ~]# docker pull nginx:latest         # 如果不写 tag,默认latest
Using default tag: latest
Trying to pull repository docker.io/library/nginx ... 
latest: Pulling from docker.io/library/nginx
b4d181a07f80: Pull complete  # 分层下载:docker image的核心 联合文件系统
66b1c490df3f: Pull complete 
d0f91ae9b44c: Pull complete 
baf987068537: Pull complete 
6bbc76cbebeb: Pull complete 
32b766478bc2: Pull complete 
Digest: sha256:353c20f74d9b6aee359f30e8e4f69c3d7eaea2f610681c4a95849a2fd7c497f9 # 签名
Status: Downloaded newer image for docker.io/nginx:latest # 真实地址
```

### docker rmi 删除镜像

> 删除可以依据id删除,或者镜像名称删除

```shell
[root@VM-24-20-centos ~]# docker rmi -f  容器id                  #删除指定的容器 
[root@VM-24-20-centos ~]# docker rmi -f  容器id 容器id 容器id     #删除多个容器
# 可选项
-f 强制删除
# 骚操作
# 1.递归删除全部的镜像 $() 传递参数，-aq 查询所有镜像
docker rmi -f $(docker images -aq)
```

## 容器命令

我们有了镜像才可以创建容器，容器是基于镜像创建的

### 新建容器并启动

```shell
docker run [可选参数] image_name||image_id
# 参数说明
--name   容器名称 nginx1 nginx2,用来区分容器
-d       后台方式运行
-it      使用交互方式运行，经如容器查看内容
-p 8080:80       制定容器端口,本机的8080端口映射容器内部的80端口

# 启动并进入容器

```

























