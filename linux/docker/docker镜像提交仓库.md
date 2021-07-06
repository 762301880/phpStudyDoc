# 1. 镜像 提交dockerhub仓库

## 1.1 登录仓库

```shell
[root@VM-28-119-centos /]# docker login
# 登录你的Docker ID推拉图像从Docker Hub。如果你没有Docker ID，请到https://hub.docker.com创建一个。
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: yaoliuyang # 输入用户名
Password:  # 输入密码
Login Succeeded # 登录成功
```

## 1.2   查看自己的镜像

```shell
docker images
# 显示如下
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
php                 7.4.3               8fcb3668bb27        10 minutes ago      494 MB
```

## 1.3 查看正在运行的镜像容器&&构建新镜像

```shell
docker ps -a 
# 构建新镜像		
docker commit yaoliuyang/php
# 查看构建的镜像
docker images
```

## 1.4 将构建的镜像提交到远程dockerhub库

```shell
docker push yaoliuyang/php:latest # latest版本号
```

