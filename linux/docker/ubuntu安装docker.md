# 一、使用apt命令安装

- w3schoole-ubntu安装docker[文档](https://www.w3cschool.cn/docker/ubuntu-docker-install.html)

## 1.1 Docker 要求 Ubuntu 系统的内核版本高于 3.10 ，查看本页面的前提条件来验证你的 Ubuntu 版本是否支持 Docker。

- 通过 uname -r 命令查看你当前的内核版本

```shell
ubuntu@VM-123-64-ubuntu:~$ uname -r
4.15.0-142-generic
```

## 1.2 更新apt包

```shell
ubuntu@VM-123-64-ubuntu:~$ sudo apt-get update
```

## 1.3 安装docker

```shell
# sudo apt install -y docker.io
ubuntu@VM-123-64-ubuntu:~$ sudo apt-get install -y docker.io
```

## 1.4 启动docker

```shell
sudo systemctl start docker
```

## 1.5 使用docker 所有的命令一定要在前面加入sudo ,解决方案

1. 添加docker用户组

```shell
sudo groupadd docker
```

2. 将登陆用户加入到docker用户组中

```shell
sudo gpasswd -a $USER docker	#USER处是你自己的用户名
ubuntu@VM-123-64-ubuntu:~$ sudo gpasswd -a ubuntu docker
```

3. 更新用户组

```shell
newgrp docker
```

# 二、删除docker

```shell
sudo apt-get remove --auto-remove docker

# 使用dpkg查询已安装包，针对性删除
# 查询相关软件包
dpkg -l | grep docker
# 删除这个包
sudo apt remove --purge dock.io
```

