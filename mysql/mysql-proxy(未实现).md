# 资料

| 名称               | 地址                                                         |
| ------------------ | ------------------------------------------------------------ |
| 网络博客           | [link](https://blog.csdn.net/weixin_43611106/article/details/122011691)  [link](https://github.com/Meituan-Dianping/DBProxy/blob/master/doc/QUICK_START.md)  [link](https://zhjwpku.com/2018/01/16/mysql-proxy-alternatives.html) |
| mysql proxy github | [link](https://github.com/mysql/mysql-proxy)                 |
| 官方下载地址       | [link](https://dev.mysql.com/downloads/mysql-proxy/)         |
| 官方文档           | [link](https://downloads.mysql.com/docs/mysql-proxy-en.pdf)  |

**简介**

> MySQL Proxy 是一个用于在 MySQL 服务器和客户端之间进行代理的工具，它可以用于实现负载均衡、故障转移、查询分析等功能。下面是 MySQL Proxy 的安装和使用步骤
>
> <font color='red'>需要注意的是，MySQL Proxy的开发已经停止，并且不再维护。官方建议使用MySQL Router来实现类似的功能。MySQL Router是MySQL官方提供的一个高可用性和负载均衡的代理工具，它可以将客户端的请求转发到多个MySQL服务器上。</font>

## **下载**

> [点击下载地址](https://downloads.mysql.com/archives/proxy/) 下载对应的版本

![image-20230927142244383](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20230927142244383.png)

**linux下载**

```shell
wget https://downloads.mysql.com/archives/get/p/21/file/mysql-proxy-0.8.5-linux-glibc2.3-x86-64bit.tar.gz

# 解压

tar -zxvf mysql-proxy-0.8.5-linux-glibc2.3-x86-64bit.tar.gz
```



```shell
# 下载 MySQL Proxy：你可以从官方网站下载 MySQL Proxy 的最新版本。下载地址：https://dev.mysql.com/downloads/mysql-proxy/

# 转移目录
mv mysql-proxy-0.8.5-linux-glibc2.3-x86-64bit/ /usr/local/mysql-proxy
```



**查看帮助文档**

```php
root@yaoliuyang:/usr/local/mysql-proxy/bin# ./mysql-proxy --help
Usage:
  mysql-proxy [OPTION...] - MySQL Proxy

Help Options:
  -?, --help                                              Show help options
  --help-all                                              Show all help options
  --help-proxy                                            Show options for the proxy-module

Application Options:
  -V, --version                                           Show version
  --defaults-file=<file>                                  configuration file
  --verbose-shutdown                                      Always log the exit code when shutting down
  --daemon                                                Start in daemon-mode
  --user=<user>                                           Run mysql-proxy as user
  --basedir=<absolute path>                               Base directory to prepend to relative paths in the config
  --pid-file=<file>                                       PID file in case we are started as daemon
  --plugin-dir=<path>                                     path to the plugins
  --plugins=<name>                                        plugins to load
  --log-level=(error|warning|info|message|debug)          log all messages of level ... or higher
  --log-file=<file>                                       log all messages in a file
  --log-use-syslog                                        log all messages to syslog
  --log-backtrace-on-crash                                try to invoke debugger on crash
  --keepalive                                             try to restart the proxy if it crashed
  --max-open-files                                        maximum number of open files (ulimit -n)
  --event-threads                                         number of event-handling threads (default: 1)
  --lua-path=<...>                                        set the LUA_PATH
  --lua-cpath=<...>                                       set the LUA_CPATH
      
      
 # 查看所有的命令
root@yaoliuyang:/usr/local/mysql-proxy/bin# ./mysql-proxy --help-all
Usage:
  mysql-proxy [OPTION...] - MySQL Proxy

Help Options:
  -?, --help                                              Show help options
  --help-all                                              Show all help options
  --help-proxy                                            Show options for the proxy-module

proxy-module
  -P, --proxy-address=<host:port>                         listening address:port of the proxy-server (default: :4040)
  -r, --proxy-read-only-backend-addresses=<host:port>     address:port of the remote slave-server (default: not set)
  -b, --proxy-backend-addresses=<host:port>               address:port of the remote backend-servers (default: 127.0.0.1:3306)
  --proxy-skip-profiling                                  disables profiling of queries (default: enabled)
  --proxy-fix-bug-25371                                   fix bug #25371 (mysqld > 5.1.12) for older libmysql versions
  -s, --proxy-lua-script=<file>                           filename of the lua script (default: not set)
  --no-proxy                                              don't start the proxy-module (default: enabled)
  --proxy-pool-no-change-user                             don't use CHANGE_USER to reset the connection coming from the pool (default: enabled)
  --proxy-connect-timeout                                 connect timeout in seconds (default: 2.0 seconds)
  --proxy-read-timeout                                    read timeout in seconds (default: 8 hours)
  --proxy-write-timeout                                   write timeout in seconds (default: 8 hours)

Application Options:
  -V, --version                                           Show version
  --defaults-file=<file>                                  configuration file
  --verbose-shutdown                                      Always log the exit code when shutting down
  --daemon                                                Start in daemon-mode
  --user=<user>                                           Run mysql-proxy as user
  --basedir=<absolute path>                               Base directory to prepend to relative paths in the config
  --pid-file=<file>                                       PID file in case we are started as daemon
  --plugin-dir=<path>                                     path to the plugins
  --plugins=<name>                                        plugins to load
  --log-level=(error|warning|info|message|debug)          log all messages of level ... or higher
  --log-file=<file>                                       log all messages in a file
  --log-use-syslog                                        log all messages to syslog
  --log-backtrace-on-crash                                try to invoke debugger on crash
  --keepalive                                             try to restart the proxy if it crashed
  --max-open-files                                        maximum number of open files (ulimit -n)
  --event-threads                                         number of event-handling threads (default: 1)
  --lua-path=<...>                                        set the LUA_PATH
  --lua-cpath=<...>                                       set the LUA_CPATH     
```

**对应配置文件**

> <font color='red'>配置文件需要设置权限为 0660</font>  

```php
root@yaoliuyang:/usr/local/mysql-proxy# cat proxy.cnf 
    
    
[mysql-proxy]
user=root
proxy-address=0.0.0.0:4406
proxy-backend-addresses=60.204.148.255:3306
proxy-backend-username=root
proxy-backend-password=yaoliuyang
proxy-read-only-backend-addresses=60.204.148.255:3307
admin-username=root
admin-password=yaoliuyang
# 进程方式启动
#daemon=true
# mysql-proxy故障时会尝试重启
keepalive=true
log-file=/usr/local/mysql-proxy/roxy.log
```

**设置权限**

```shell
chmod 0660 proxy.cnf 
```



**启动**

```php
root@yaoliuyang:/usr/local/mysql-proxy/bin# ./mysql-proxy --defaults-file=../proxy.cnf
```

## 补充

## 端口被占用情况

> 使用netstat查看占用的端口

```php
root@yaoliuyang:/usr/local/mysql-proxy/bin# netstat -anp | grep 4406
tcp        0      0 0.0.0.0:4406            0.0.0.0:*               LISTEN      842400/mysql-proxy  
tcp        0      0 192.168.0.161:4406      14.221.51.66:2783       ESTABLISHED 842400/mysql-proxy  
root@yaoliuyang:/usr/local/mysql-proxy/bin# kill 842400
```

#  不推荐使用暂未研究清楚

