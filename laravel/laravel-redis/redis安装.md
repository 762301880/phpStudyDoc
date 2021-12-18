#   [linux安装](https://www.runoob.com/redis/redis-install.html)

> 请在[redis官网](https://redis.io/download)下载开发包：https://redis.io/download
>
> 官网提供了三个版本 ***Unstable(不稳定)***,***Stable(稳定版本)***,**Docker Hub(docker版本)**
>
> 推荐使用稳定版本 

**下载官网提供的稳定版本**

```shell
# 从源代码 下载、解压和编译Redis： 推荐将redis放在 /usr/local目录下编译
wget https://download.redis.io/releases/redis-6.2.6.tar.gz 
tar -zxzf redis-6.2.6.tar.gz
cd redis-6.2.6
make

# 执行完 make 命令后，redis-6.0.8 的 src 目录下会出现编译后的 redis 服务程序 redis-server，还有用于测试的客户端程序 redis-cli：下面启动 redis 服务 进入编译过后的redis目录 

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

**[redis.conf配置常用介绍](https://www.runoob.com/redis/redis-conf.html)**

> 更多配置：https://www.runoob.com/redis/redis-conf.html

```shell
# redis.conf配置 
/usr/local/redis-6.2.6/redis.conf

daemonize no # Redis 默认不是以守护进程的方式运行，可以通过该配置项修改，使用 yes 启用守护进程（Windows 不支持守护线程的配置为 no ）

# 端口 默认端口如果有需要更换请自行修改
port 6379
# redis密码 设置 Redis 连接密码，如果配置了连接密码，客户端在连接 Redis 时需要通过 AUTH <password> 命令提供密码，默认关闭
requirepass 123456 # （搜/requirepass foobared）设置Redis密码
	
timeout 300  # 当客户端闲置多长秒后关闭连接，如果指定为 0 ，表示关闭该功能

bind 127.0.0.1   # 绑定的主机地址	bind指定网段远程访问redis，注释就没有限制了
```

**redis设置后台启动**

```shell
# 将 redis.conf 下的 daemonize 改为yes

cat redis.conf | grep daemonize # 查询是否修改

# 进入到src目录下执行

./redis-server ../redis.conf --port 6379

# 查看端口是否启用成功
netstat -anp |grep 6379
```

