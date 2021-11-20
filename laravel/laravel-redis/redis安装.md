#   [linux安装](https://www.runoob.com/redis/redis-install.html)

> 请在[redis官网](https://redis.io/download)下载开发包：https://redis.io/download
>
> 官网提供了三个版本 ***Unstable(不稳定)***,***Stable(稳定版本)***,**Docker Hub(docker版本)**
>
> 推荐使用稳定版本 

**下载官网提供的稳定版本**

```shell
# 从源代码 下载、解压和编译Redis： 推荐将redis放在 /usr/local目录下
wget https://download.redis.io/releases/redis-6.2.6.tar.gz 
tar -zxzf redis-6.2.6.tar.gz
cd redis-6.2.6
make

#执行完 make 命令后，redis-6.0.8 的 src 目录下会出现编译后的 redis 服务程序 redis-server，还有用于测试的客户端程序 redis-cli：下面启动 redis 服务 进入编译过后的redis目录

cd src
/redis-server

# 注意这种方式启动 redis 使用的是默认配置。也可以通过启动参数告诉 redis 使用指定配置文件使用下面命令启动。
 cd src
 ./redis-server ../redis.conf

# redis.conf 是一个默认的配置文件。我们可以根据需要使用自己的配置文件。启动 redis 服务进程后，就可以使用测试客户端程序 redis-cli 和 redis 服务交互了。 比如：

 cd src
./redis-cli
redis> set foo bar
OK
redis> get foo
"bar"
```

