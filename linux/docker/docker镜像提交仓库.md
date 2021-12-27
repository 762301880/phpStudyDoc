# 一、 镜像 提交dockerhub仓库

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

- 参考资料[利用 commit 理解镜像构成](https://yeasy.gitbook.io/docker_practice/image/commit)

### docker commit  提交一个容器为新的镜像

> 次命令常用于提交自己构建的容器

```shell
docker commit 提交容器成为一个新的副本
docker commit -m '提交的描述信息' -a '作者信息'  需要提交的容器id  个人仓库名/目标镜像名:tag
# 例如
docker commit -m 'include swoole&vim' -a 'yaoliuyang' cd1  yaoliuyang/php:7.4-fpm:
```

## 1.4 将构建的镜像提交到远程dockerhub库

```shell
docker push 需要推送的地址名/镜像名:版本号
# 使用
docker push yaoliuyang/php:latest # latest版本号
```

# 二、镜像提交到阿里云仓库

## 2.1 创建阿里云镜像仓库

- 打开阿里云[镜像容器服务](https://cr.console.aliyun.com/cn-beijing/instances)

<img src="https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210706163529366.png" alt="image-20210706163529366" style="zoom:50%;" />

- 添加新仓库

<img src="https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210706163700186.png" alt="image-20210706163700186" style="zoom:50%;" />

- 选择仓库存储类型

![image-20210706163835217](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210706163835217.png)



## 2.2 使用方式

### 2.2.1  登录阿里云Docker Registry

```shell
$ docker login --username=76230****@qq.com registry.cn-beijing.aliyuncs.com
```

用于登录的用户名为阿里云账号全名，密码为开通服务时设置的密码。

您可以在访问凭证页面修改凭证密码。

### 2.2.2 从Registry中拉取镜像

```shell
$ docker pull registry.cn-beijing.aliyuncs.com/yaoliuyang/php:[镜像版本号]
```

### 2.2.3  将镜像推送到Registry

```shell
$ docker login --username=76230****@qq.com registry.cn-beijing.aliyuncs.com$ docker tag [ImageId] registry.cn-beijing.aliyuncs.com/yaoliuyang/php:[镜像版本号]$ docker push registry.cn-beijing.aliyuncs.com/yaoliuyang/php:[镜像版本号]
```

请根据实际镜像信息替换示例中的[ImageId]和[镜像版本号]参数。

### 2.2.4  选择合适的镜像仓库地址

从ECS推送镜像时，可以选择使用镜像仓库内网地址。推送速度将得到提升并且将不会损耗您的公网流量。

如果您使用的机器位于VPC网络，请使用 registry-vpc.cn-beijing.aliyuncs.com 作为Registry的域名登录。

### 2.2.5  示例

使用"docker tag"命令重命名镜像，并将它通过专有网络地址推送至Registry。

```shell
$ docker imagesREPOSITORY                                                         TAG                 IMAGE ID            CREATED             VIRTUAL SIZEregistry.aliyuncs.com/acs/agent                                    0.7-dfb6816         37bb9c63c8b2        7 days ago          37.89 MB$ docker tag 37bb9c63c8b2 registry-vpc.cn-beijing.aliyuncs.com/acs/agent:0.7-dfb6816
```

使用 "docker push" 命令将该镜像推送至远程。

```shell
$ docker push registry-vpc.cn-beijing.aliyuncs.com/acs/agent:0.7-dfb6816
```