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
# 1.递归删除全部的镜像 $() 传递参数，-aq 查询所有镜像 此命令限于linux环境使用
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
# 运行容器
ubuntu@VM-123-64-ubuntu:~$ docker run --name nginx -itd -p 8080:80 nginx
3c3dfa40d92b45851f5b590df51c85b5e4aac3bee7bcc063515919b2964cfd36
```

### 列出所有运行的容器

```shell
# docker ps 命令 列出当前正在运行中的容器
-a # 列出当前正在运行的容器+带出历史运行过的容器
-n=?  #显示最近创建的容器
-q # 列出当前正在运行中的容器的id ，docker ps -aq 显示当前所有运行容器的id 

ubuntu@VM-123-64-ubuntu:~$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
3c3dfa40d92b        nginx               "/docker-entrypoint.…"   36 seconds ago      Up 35 seconds       0.0.0.0:8080->80/tcp nginx
```

### 进入容器

```shell
docker exec -it 容器id /bin/bash
```

### 删除容器

```shell
# 删除指定的容器
docker rm 容器id||容器名称
# 删除全部的容器 此命令限于linux环境使用
docker rm -f $(docker ps -aq)
```

### 退出容器

```shell
exit # 直接容器停止并推出
Ctrl + P + Q #容器不停止退出
```

### 启动或停止容器

```shell
docker stop 容器id  # 停止容器
docker restart 容器id  # 重启容器
docker start 容器id  # 启动容器
docker kall 容器id  # 强制停止当前容器
```

### 修改容器的名称

```php
/**
 * 修改容器的名称  
 * 参考资料：https://www.west.cn/docs/58259.html
 */
docker rename +原有容器的名称(容器id)
# 示例 将name为pedantic_dhawan的容器修改为local_hyperf_test
PS C:\Users\Administrator> docker ps -a
CONTAINER ID   IMAGE                                   COMMAND     CREATED        STATUS                       PORTS                                       NAMES
452bf88b2512   hyperf/hyperf:7.4-alpine-v3.11-swoole   "/bin/sh"   21 hours ago   Up 4 minutes                 0.0.0.0:9802->9802/tcp, :::9802->9802/tcp   hf_industrial_park
158dc26af455   hyperf/hyperf:7.4-alpine-v3.11-swoole   "/bin/sh"   23 hours ago   Exited (255) 7 minutes ago   0.0.0.0:9501->9501/tcp, :::9501->9501/tcp   hf_industrialBusiness
21cb792091da   hyperf/hyperf:7.4-alpine-v3.11-swoole   "/bin/sh"   23 hours ago   Exited (255) 7 minutes ago   0.0.0.0:9801->9801/tcp, :::9801->9801/tcp   pedantic_dhawan
PS C:\Users\Administrator> docker rename 21 local_hyperf_test
PS C:\Users\Administrator> docker ps -a
CONTAINER ID   IMAGE                                   COMMAND     CREATED        STATUS                       PORTS                                       NAMES
452bf88b2512   hyperf/hyperf:7.4-alpine-v3.11-swoole   "/bin/sh"   21 hours ago   Up 4 minutes                 0.0.0.0:9802->9802/tcp, :::9802->9802/tcp   hf_industrial_park
158dc26af455   hyperf/hyperf:7.4-alpine-v3.11-swoole   "/bin/sh"   23 hours ago   Exited (255) 8 minutes ago   0.0.0.0:9501->9501/tcp, :::9501->9501/tcp   hf_industrialBusiness
21cb792091da   hyperf/hyperf:7.4-alpine-v3.11-swoole   "/bin/sh"   23 hours ago   Exited (255) 8 minutes ago   0.0.0.0:9801->9801/tcp, :::9801->9801/tcp   local_hyperf_test    
```

[**查看容器占用的内存**](https://www.php.cn/docker/446312.html)

> 官方[stats命令详解](https://docs.docker.com/engine/reference/commandline/stats/)

```dockerfile
# docker stats [容器名称]
[root@VM-16-5-centos ~]# docker stats mysql

CONTAINER           CPU %               MEM USAGE / LIMIT       MEM %               NET I/O             BLOCK I/O           PIDS
mysql               0.15%               369.6 MiB / 1.795 GiB   20.11%              1.19 MB / 844 kB    500 MB / 13.7 MB    38
...............


# 同时查看多个 按名称和 id 在多个容器上运行。(多个容器名称或者idy)
[root@VM-16-5-centos ~]# docker stats mysql jd laravel_study
CONTAINER           CPU %               MEM USAGE / LIMIT       MEM %               NET I/O             BLOCK I/O           PIDS
mysql               0.13%               369.6 MiB / 1.795 GiB   20.11%              4.19 MB / 2.9 MB    500 MB / 13.7 MB    38
jd                  0.60%               377.4 MiB / 1.795 GiB   20.53%              0 B / 0 B           29.9 GB / 29.4 MB   164
laravel_study       0.02%               52.26 MiB / 1.795 GiB   2.84%               11 MB / 9.61 MB     11.8 GB / 30.6 MB   18

# 列名	描述
CONTAINER ID 和 Name	容器的 ID 和名称
CPU % 和 MEM %	容器正在使用的主机 CPU 和内存的百分比
MEM USAGE / LIMIT	容器正在使用的总内存，以及允许使用的内存总量
NET I/O	容器通过其网络接口发送和接收的数据量
BLOCK I/O	容器从主机上的块设备读取和写入的数据量
PIDs	容器创建的进程或线程数
```



## 常用命令

### 查看日志命令

```shell
[root@VM-23-6-centos ~]# docker logs --help

Usage:  docker logs [OPTIONS] CONTAINER

Fetch the logs of a container

Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output
      --help           Print usage
      --since string   Show logs since timestamp
      --tail string    Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     Show timestamps
 # 使用
 [root@VM-23-6-centos ~]# docker logs -f  -t  --tail 查询数量 容器id
 - tf # 显示日至
 - tail  number  # 要显示日志条数
 # 使用示例  注意是已经启动的容器id不是镜像di
 [root@VM-16-5-centos ~]# docker logs 容器id
2021-12-27 00:28:51.474+0000 [id=387]	SEVERE	h.model.UpdateCenter$DownloadJob#run: Failed to install gitlab-hook
java.io.IOException: Failed to load: Gitlab Hook Plugin (1.4.2) 显示报错日志
 .................

```

### 查看容器中进程的信息

```shell
[root@VM-23-6-centos ~]# docker top 容器id
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                23021               23006               0                   20:31               pts/2               00:00:00            php-fpm: master process (/usr/local/etc/php-fpm.conf)
33                  23046               23021               0                   20:31               pts/2               00:00:00            php-fpm: pool www
33                  23047               23021               0                   20:31               pts/2               00:00:00            php-fpm: pool www

```

### 查看镜像的元数据

```shell
root@VM-23-6-centos ~]# docker inspect --help

Usage:  docker inspect [OPTIONS] NAME|ID [NAME|ID...]

Return low-level information on Docker objects

Options:
  -f, --format string   Format the output using the given Go template
      --help            Print usage
  -s, --size            Display total file sizes if the type is container
      --type string     Return JSON for specified type
      
# 基本使用      
docker inspect 容器id
```

### 进入当前正在运行中的容器

```shell
# 我们通常容器都是使用后台方式运行的需要进入容器修改一些配置
# 命令
docker exec -it 容器id  /bin/bash
```

### 将容器内部的文件考出文件外部(本地主机)

```shell
docker cp 容器id:容器内路径  本地主机路径
# 使用势力--请在容器外部执行
[root@VM-23-6-centos ~]# docker cp 125:/usr  ~
[root@VM-23-6-centos ~]# ls
usr
[root@VM-23-6-centos ~]# cd usr/
[root@VM-23-6-centos usr]# ls
bin  games  include  lib  local  sbin  share  src

# windows使用示例
docker cp 容器id:/etc/nginx/conf.d/ c:/      # 将容器内nginx的配置文件拷贝到本地 
```













