# 资料

| 名称          | 地址                                           |
| ------------- | ---------------------------------------------- |
| redis官方文档 | [link](http://www.redis.cn/documentation.html) |

#  redis安装

## [linux安装](https://www.runoob.com/redis/redis-install.html)

> 请在[redis官网](https://redis.io/download)下载开发包：https://redis.io/download
>
> 官网提供了三个版本 ***Unstable(不稳定)***,***Stable(稳定版本)***,**Docker Hub(docker版本)**
>
> 推荐使用稳定版本 

**下载官网提供的稳定版本**

```shell
# 从源代码 下载、解压和编译Redis： 推荐将redis放在 /usr/local目录下编译
cd /usr/local
wget https://download.redis.io/releases/redis-6.2.6.tar.gz 
tar -zxvf redis-6.2.6.tar.gz && cd redis-6.2.6
make
make install #执行安装 redis_server redis-cli 在bin目录     #指定路径 make PREFIX=/usr/local/redis/ install

# 执行完 make 命令后，redis-6.0.8 的 src 目录下会出现编译后的 redis 服务程序 redis-server，还有用于测试的客户端程序 redis-cli：下面启动 redis 服务 进入编译过后的redis目录 

# 注意这种方式启动 redis 使用的是默认配置。也可以通过启动参数告诉 redis 使用指定配置文件使用下面命令启动。
 cd src
 ./redis-server ../redis.conf        # 通过指定的配置文件启动

# redis.conf 是一个默认的配置文件。我们可以根据需要使用自己的配置文件。启动 redis 服务进程后，就可以使用测试客户端程序 redis-cli 和 redis 服务交互了。 比如：

 cd src
./redis-cli
redis> set foo bar
OK
redis> get foo
"bar"
```

**配置服务启动**

| 名称 | 地址                                                         |
| ---- | ------------------------------------------------------------ |
| 博客 | [link](http://t.zoukankan.com/murenhui-p-8898238.html) [link](https://blog.csdn.net/u014163312/article/details/123650944) [link](https://blog.csdn.net/x1128559250/article/details/95511171?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-5-95511171-blog-123650944.pc_relevant_recovery_v2&spm=1001.2101.3001.4242.4&utm_relevant_index=8) |

> 启动 **/etc/init.d/redis start**

```shell
cd /usr/local/redis-6.2.6/utils     # 进入编译后的utils文件
sudo cp redis_init_script /etc/init.d/redis         # 拷贝文件
sudo vim /etc/init.d/redis      # 记得这个要切换root用户编辑
# /etc/init.d/redis 
REDISPORT=6379
EXEC=/usr/local/bin/redis-server    # 修改为自己对应的
CLIEXEC=/usr/local/bin/redis-cli     # 修改为自己对应的

PIDFILE=/var/run/redis_${REDISPORT}.pid
CONF="/etc/redis/${REDISPORT}.conf"      # 此配置文件对应/usr/local/redis-6.2.6中的redis.conf  修改名称并备份即可 cp redis.conf cp redis.conf.cp && cp redis.conf 6379.conf
chmod a+x  /etc/init.d/redis  
# 重新载入配置  systemctl daemon-reload
systemctl daemon-reload
service redis start 
systemctl start redis.service


# ubuntu 设置开机自启动去的是 /etc/init.d/redis配置
sudo update-rc.d redis defaults # 加载到系统自启动文件
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

## windows安装redis

> 如果我们使用的是**phpstudy**这种集成开发环境自带的就有redis,开启之后我们只需要配置系统环境变量即可在终端中使用
>
> **配置环境变量**
>
> 找到右键此电脑找到环境变量,在系统变量中添加**redis目录位置** `例如` **D:\phpstudy_pro\Extensions\redis3.0.504**
>
> 然后终端中使用**redis-cli**即可再终端中使用

# 云redis

> 今天无意中找到一款云redis

**资料**

| 名称        | 地址                               |
| ----------- | ---------------------------------- |
| 云redis官网 | [link](https://app.redislabs.com/) |
|             |                                    |

> 怎么配置的不详解就像你注册qq账号这么简单

![1646129429(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/qhmbrvu21RAToQC.png)

## 项目中配置云reids

**laravel配置**

> 在**.env**中配置 端口还有密码

```shell
REDIS_HOST=redis-13822.c258.us-east-1-4.ec2.cloud.redislabs.com
REDIS_PASSWORD='********' # 自定义的密码
REDIS_PORT=13822
```

**用ip地址连接**

> <font color='red'>上一步的域名:redis-13822.c258.us-east-1-4.ec2.cloud.redislabs.com</font>
>
> 可以用ping 命令测试出ip地址然后讲上一步的域名替换为ip地址连接

```shell
[root@VM-16-5-centos plugins]# ping redis-13822.c258.us-east-1-4.ec2.cloud.redislabs.com
PING redis-13822.c258.us-east-1-4.ec2.cloud.redislabs.com (3.81.36.161) 56(84) bytes of data.

# 3.81.36.161
```

