# 讲解

> 通过 --link 可以解决网络联通问题(通过名字就可以访问) **例如ping php**

**拉取php镜像** && nginx 镜像

```dockerfile
[root@VM-16-5-centos ~]# docker pull php:7.4.3-fpm
[root@VM-16-5-centos ~]# docker nginx
# 启动php
[root@VM-16-5-centos ~]# docker run --name  myphp-fpm -v ~/nginx/www:/www  -d php:7.4.3-fpm
# 查看启动的php 可以发现已经开启了9000 tcp端口
[root@VM-16-5-centos ~]# docker ps -a | grep myphp-fpm
CONTAINER ID        IMAGE                                   COMMAND                  CREATED             STATUS              PORTS                                              NAMES
bf97c4677281        php:7.4.3-fpm                           "docker-php-entryp..."   14 seconds ago      Up 14 seconds       9000/tcp                                           myphp-fpm

# 启动nginx --link myphp-fpm:php \
    docker run --name runoob-php-nginx -p 8083:80 -d \
    -v ~/nginx/www:/usr/share/nginx/html:ro \
    -v ~/nginx/conf/conf.d:/etc/nginx/conf.d:ro \
    --link myphp-fpm:php \
    nginx

# 进入容器后ping php        
root@dcc67001ebbe:/# ping php     
PING php (172.17.0.7) 56(84) bytes of data.
```

> --link 
>
> 思考一个场景,我们编写了一个微服务,database  url=ip:,项目不重启,数据库ip换掉了,我们希望可以处理这个问题,可以名字来进行访问容器?

```shell
# 下载tomcat镜像
[root@VM-16-5-centos ~]# docker pull tomcat:9.0



# 启动容器tomcat01
[root@VM-16-5-centos ~]# docker run -d -P --name tomcat01 b8


# 启动容器tomcat02
[root@VM-16-5-centos ~]# docker run -d -P --name tomcat02 b8


# 尝试ping服务tomcat01
[root@VM-16-5-centos ~]# docker exec -it tomcat02 ping tomcat01
ping: tomcat01: Name or service not known


# 此时是ping不通的如何可以解决呢?
[root@VM-16-5-centos ~]# docker run -d -P --name tomcat03 --link tomcat02  b8


# 如果没有ping命令请自行在容器内部安装(ununtu:apt install iputils-ping)
[root@VM-16-5-centos ~]# docker exec -it tomcat03 ping tomcat02
PING tomcat02 (172.17.0.6) 56(84) bytes of data.
64 bytes from tomcat02 (172.17.0.6): icmp_seq=1 ttl=64 time=0.077 ms
.....

#-------------------------------反向ping--------------start-----------------------------
# 反向可以ping通吗?  不可以 因为没有配置
[root@VM-16-5-centos ~]# docker exec -it tomcat02 ping tomcat03
ping: tomcat03: Name or service not known


[root@VM-16-5-centos ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
51354af29e83        bridge              bridge              local           # bridge  docker0网络
2ff22bd34a38        host                host                local
6072e5fef379        none                null                local
# 查看网卡信息   探究:inspect
[root@VM-16-5-centos ~]# docker network inspect 51354af29e83
[
    {
        "Name": "bridge",
        "Id": "51354af29e83ad6a4d35d251aa85ecba09606ed9ef6d5e0c48a78ff7534da002",
        "Created": "2022-08-22T17:53:14.869106123+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Containers": {
            "667f4dc97bef35562ce3f9c57993db3ec95a8ad9c2e28c71731aa04c63f04813": {
                "Name": "qinglong",
                "EndpointID": "a9664939d1aeb29503ca52dd017632df8f4d397df096d00ab760536aface45d4",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            },
            "6b19e5674ee69b6e3b066260b0f5bf5f908c256b9c171f189001c459eb1f8d13": {
                "Name": "tomcat01",
                "EndpointID": "6cef53a17d3be1bcafa550416ce29b52594bcc864f37e96c3df7b8a244faee6d",
                "MacAddress": "02:42:ac:11:00:05",
                "IPv4Address": "172.17.0.5/16",
                "IPv6Address": ""
            },
            "6f940ab113d42a2605dffb2c08fa5f11ec30556c70fac12c459fbcf3c26a7034": {
                "Name": "tomcat03",
                "EndpointID": "8e3e5350bb6078da6932d0a5686f3bbc3b73b1794366d5fcdaaabebca943d180",
                "MacAddress": "02:42:ac:11:00:07",
                "IPv4Address": "172.17.0.7/16",
                "IPv6Address": ""
            },
            "c087b076d24fc716e2afd1f78481113cdb6959e9eadc0f40cf757dbbac883d59": {
                "Name": "laravel_study",
                "EndpointID": "6e3fb0ba66dc866ac9647be36730b73a4c442292a2901d818880041cdcec4ead",
                "MacAddress": "02:42:ac:11:00:03",
                "IPv4Address": "172.17.0.3/16",
                "IPv6Address": ""
            },
            "c3f2b59192579859e8f39fac3220eb01593635041bdc1e191801774ddacad431": {
                "Name": "jenkins",
                "EndpointID": "11fd2b42529d334fd1ff616ec4237d4a39f9a62de3645f68384d79ed27e71e2b",
                "MacAddress": "02:42:ac:11:00:04",
                "IPv4Address": "172.17.0.4/16",
                "IPv6Address": ""
            },
            "f909ea3ce825f34d0b710302e5b9731a5740fbc1cf5cc9531e9490b00589ae1f": {
                "Name": "tomcat02",
                "EndpointID": "05f4e7e321b22cbfdc051779a2ef3b45aca0c64f5e0075e75cf46462f24710f2",
                "MacAddress": "02:42:ac:11:00:06",
                "IPv4Address": "172.17.0.6/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]

# 查看03的hosts文件,  hosts文件配置绑定
[root@VM-16-5-centos ~]# docker exec -it tomcat03 cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.17.0.6	tomcat02 f909ea3ce825       # 访问 tomcat02 转发到 172.17.0.6 所以可以ping通
172.17.0.7	6f940ab113d4


#-------------------------------反向ping--------------end-----------------------------

本质探究:   --link #就是我们在hosts配置中增加了一个tomcat02映射
我们现在玩Docker已经不建议使用--link了!
自定义网络!不使用docker0!
docker0问题:它不支持容器名连接访问!
```





















