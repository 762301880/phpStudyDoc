# 一、说明

> 由于研究本地配置nginx服务的时候把nginx玩坏了，卸载重装发现无论如何都是
>
> 无法成功复原nginx服务。

# 二、操作删除nginx

## 2.1 卸载nginx，及其配置文件

```shell
sudo apt-get --purge remove nginx
```

## 2.2 自动全部移除不使用的软件包

```shell
sudo apt-get autoremove
```

## 2.3 列出与nginx相关的软件

```shell
dpkg --get-selections|grep nginx
```

### 2.3.1 删除查询出来的与nginx相关的软件

```shell
sudo apt-get --purge remove +查询出来的软件名
```

## 2.4 查看正在执行的nginx进程

```shell
ps -ef |grep nginx
```

### 2.4.1 杀死正在执行的进程

```shell
sudo kill  -9 xxx:xxx表示的是进程代码
```

## 2.5 全局找出与nginx相关的文件

```shell
sudo  find  /  -name  nginx*
```

### 2.5.1 依次删除查找出来与nginx相关的软件

```shell
sudo rm -rf xxx:xxx表示文件路径
```































