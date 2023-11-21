# 一、容器内部安装

## 1.1 首先进入容器内部

```shell
docker exec -it +容器id /bin/bash
```

## 1.2 跟新apt源

```shell
apt update
```

## 1.3 安装vim

```shell
apt install vim 
```

## 1.4 查看是否安装成功

```shell
vim --version
# 显示结果如下
VIM - Vi IMproved 8.1 (2018 May 18, compiled Jun 15 2019 16:41:15)