#   [linux安装](https://www.runoob.com/redis/redis-install.html)

> 请在[redis官网](https://redis.io/download)下载开发包：https://redis.io/download
>
> 官网提供了三个版本 ***Unstable(不稳定)***,***Stable(稳定版本)***,**Docker Hub(docker版本)**
>
> 推荐使用稳定版本 

**下载官网提供的稳定版本**

```shell
# 从源代码 下载、解压和编译Redis：
$ wget https://download.redis.io/releases/redis-6.2.6.tar.gz 
$ tar -zxzf redis-6.2.6.tar.gz
$ cd redis-6.2.6
$ make
# 现在编译的二进制文件在src 目录中可用 。启动运行Redis：
$ src/redis-server
# 您可以使用内置客户端与 Redis 交互：
$ src/redis-cli
$ redis> set foo bar
$ OK
$ redis> get foo
"bar"
```

