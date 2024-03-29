# 资料

| 名称                     | 地址                                                         |
| ------------------------ | ------------------------------------------------------------ |
| Docker-从入门到实战-网络 | [link](https://yeasy.gitbook.io/docker_practice/underly/network) |



# Docker 网络

## 理解docker网络(Docker0)

>**ip addr** linux中获取当前地址

```shell
# 三个网络（网卡）
[root@VM-1-64-centos ~]# ip addr
# 本机回环地址
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:	00:00:00:00
    inet 127.0.0.1/8 scope host lo          # 127.0.0.1 本机回环地址
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
# 腾讯云内网地址	
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 52:54:00:17:c3:c1 brd ff:ff:ff:ff:ff:ff
    inet 10.186.1.64/16 brd 10.186.255.255 scope global eth0       # 10.186.1.64 腾讯云内网地址
       valid_lft forever preferred_lft forever
    inet6 fe80::5054:ff:fe17:c3c1/64 scope link 
       valid_lft forever preferred_lft forever
# docker0  地址
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:7d:f1:d6:60 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 scope global docker0             # 172.17.0.1/16  Docker0地址
       valid_lft forever preferred_lft forever
```

## 测试

> 注意没有**ip addr**命令的时候  却啥装啥 例如(docker exec -it tomcat01 apt update && apt install -y iproute2)
>
> **更新apt依赖**       **安装ipaddr**                     **安装ifconfig**                       **安装ping**
> apt update         apt install -y iproute2       apt install -y net-tools        apt install -y iputils-ping

```php
[root@VM-1-64-centos ~]# docker run -d -P --name tomcat01  tomcat

#查看容器内部网络地址 ip addr    发现容器启动的时候会得到一个 eth0@if211  ip地址   docker分配的
[root@VM-16-5-centos ~]# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
210: eth0@if211: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:05 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.5/16 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:acff:fe11:5/64 scope link 
       valid_lft forever preferred_lft forever
# 思考：linux 能不能ping通容器内部(能ping通就能连接)  实验可以看出 linux可以ping通docker容器
[root@VM-16-5-centos ~]# ping  172.17.0.5
PING 172.17.0.5 (172.17.0.5) 56(84) bytes of data.
64 bytes from 172.17.0.5: icmp_seq=1 ttl=64 time=0.048 ms
64 bytes from 172.17.0.5: icmp_seq=8 ttl=64 time=0.040 ms  
.......    
```

**原理**

> 1. 我们每启动一个docker容器,docker就会给docker容器分配一个ip,我们只要安装了docker,就会有一个网卡**docker0**
>
>    桥接模式,使用的技术是evth-pair技术!

```php
[root@VM-16-5-centos ~]# docker exec -it tomcat01 ip addr
...........
# eth0@if211       211
210: eth0@if211: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:05 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.5/16 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:acff:fe11:5/64 scope link 
       valid_lft forever preferred_lft forever
[root@VM-16-5-centos ~]# ip addr
.............
# 可以看出多出个211        
211: vethd97fbe1@if210: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether 7a:e7:8a:9e:19:c3 brd ff:ff:ff:ff:ff:ff link-netnsid 2
    inet6 fe80::78e7:8aff:fe9e:19c3/64 scope link 
       valid_lft forever preferred_lft forever       
```

> 我们发现这个容器带来的网卡,都是一对一对的,**evth-pair** 就是一对的虚拟设备接口,他们都是成对的,一端连着协议一端彼此相连
>
> 正因为有这个特向,evth-pair充当一个桥梁，连接各种虚拟网络设备的,

**容器和容器之间是否可以ping通**(可以ping通)

```php
# 创建tomcat02 容器
[root@VM-1-64-centos ~]# docker run -d -P --name tomcat02  tomcat
# 执行更新
[root@VM-16-5-centos ~]# docker exec -it tomcat02 apt update
.......
# 安装ping 
[root@VM-16-5-centos ~]# docker exec -it tomcat02 apt install -y iputils-ping 
    
# 测试ping tomcat01 
[root@VM-16-5-centos ~]# docker exec -it tomcat02 ping 172.17.0.5
PING 172.17.0.5 (172.17.0.5) 56(84) bytes of data.
64 bytes from 172.17.0.5: icmp_seq=1 ttl=64 time=0.075 ms
.......
```

**总结**

> Docker0 docker安装就有的**172.17.0.1**  相当于路由器 所有的请求都需要经过路由器转发或者发送
>
> tomcat01 和 tomcat02是一个共有的路由器,docker0
>
> 所有的容器不指定网络的情况下,都是docker0路由的,docker会给我们的容器分配一个默认的可用ip

# 自定义网络

```shell
[root@VM-16-5-centos ~]# docker network --help

Usage:  docker network COMMAND

Manage networks

Options:
      --help   Print usage

Commands:
  connect     Connect a container to a network  #将容器连接到网络
  create      Create a network # 创建网络
  disconnect  Disconnect a container from a network # 断开容器与网络的连接
  inspect     Display detailed information on one or more networks # 显示一个或多个网络的详细信息
  ls          List networks                  # 网络列表
  prune       Remove all unused networks       # 删除所有未使用的网络
  rm          Remove one or more networks       #rm移除一个或多个网络

Run 'docker network COMMAND --help' for more information on a command.
#-----------------------------
# 查看所有的docker网络列表

[root@VM-16-5-centos ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
51354af29e83        bridge              bridge              local
2ff22bd34a38        host                host                local
6072e5fef379        none                null                local
#-----------------------------
# 网络模式
bridge(默认)  : 桥接 docker 搭桥(自己搭建也使用bridge模式)
none         : 不配置网络
host         : 和宿主机共享网络
container    : 容器内可以网络联通!(用的少！局限很大)
# =============测试===================
# 我们直接启动的命令  --net bridge  而这个就是我们的docker01
[root@VM-16-5-centos ~]# docker run -d -P --name tomcat01  b8
[root@VM-16-5-centos ~]# docker run -d -P --name tomcat01 --net bridge tomcat  b8
# 上面两个是相等的  --net bridge 是默认创建的

# docker0特点:默认,域名不能访问, --link 可以打通连接

# 我们可以自定义一个网络!
[root@VM-16-5-centos ~]# docker network create --help

Usage:  docker network create [OPTIONS] NETWORK

Create a network

Options:
      --attachable             Enable manual container attachment
      --aux-address map        Auxiliary IPv4 or IPv6 addresses used by Network driver (default map[])
  -d, --driver string          Driver to manage the Network (default "bridge")
      --gateway stringSlice    IPv4 or IPv6 Gateway for the master subnet
      --help                   Print usage
      --internal               Restrict external access to the network
      --ip-range stringSlice   Allocate container ip from a sub-range
      --ipam-driver string     IP Address Management Driver (default "default")
      --ipam-opt map           Set IPAM driver specific options (default map[])
      --ipv6                   Enable IPv6 networking
      --label list             Set metadata on a network (default [])
  -o, --opt map                Set driver specific options (default map[])
      --subnet stringSlice     Subnet in CIDR format that represents a network segment
# ---------------------------------
[root@VM-16-5-centos ~]# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
28efdb5a5f68162183fff88bda77888a1c368d3e336919be04b4d4a8b3700ae9

创建了一个网络 mynet ,--subnet是他的子网,
# 查看创建的网络
[root@VM-16-5-centos ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
51354af29e83        bridge              bridge              local
2ff22bd34a38        host                host                local
28efdb5a5f68        mynet               bridge              local
6072e5fef379        none                null                local


# 查看自定义网络的详细信息-我们自己的网络就创建好了

[root@VM-16-5-centos ~]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "28efdb5a5f68162183fff88bda77888a1c368d3e336919be04b4d4a8b3700ae9",
        "Created": "2022-08-29T20:32:35.707417304+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
#============================
# 启动一个容器并绑定自定义网络
[root@VM-16-5-centos ~]# docker run -d -P --name tomcat-net-01  --net mynet  b8
f2f49914c83dea308e42839e3ddba928f5b313134286c9742b2558b720c78143
# tomcat02
[root@VM-16-5-centos ~]# docker run -d -P --name tomcat-net-02  --net mynet  b8
194d832fb4144b2d7871fb236dc3f20213f1609442330975f6d63b3b3a408367
#================================

#再次查看网络
[root@VM-16-5-centos ~]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "28efdb5a5f68162183fff88bda77888a1c368d3e336919be04b4d4a8b3700ae9",
        "Created": "2022-08-29T20:32:35.707417304+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Containers": {
            "194d832fb4144b2d7871fb236dc3f20213f1609442330975f6d63b3b3a408367": {
                "Name": "tomcat-net-02", 
                "EndpointID": "c2d216be4e7b8e75ec56c867b10a5afd3dabb37c6b774c5a69139cea41014f55",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            },
            "f2f49914c83dea308e42839e3ddba928f5b313134286c9742b2558b720c78143": {
                "Name": "tomcat-net-01",
                "EndpointID": "de56c70758fcf122c9612105b76295177a99077070242867e6277aec94e3cea8",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]

#//

[root@VM-16-5-centos ~]# docker ps -a
CONTAINER ID        IMAGE                                   COMMAND                  CREATED             STATUS              PORTS                                              NAMES
194d832fb414        b8                                      "catalina.sh run"        24 minutes ago      Up 24 minutes       0.0.0.0:32777->8080/tcp                            tomcat-net-02
f2f49914c83d        b8                                      "catalina.sh run"        25 minutes ago      Up 25 minutes       0.0.0.0:32776->8080/tcp                            tomcat-net-01

# 再次测试ping连接
[root@VM-16-5-centos ~]# docker exec tomcat-net-02 ping 192.168.0.3
PING 192.168.0.3 (192.168.0.3): 56 data bytes
64 bytes from 192.168.0.3: icmp_seq=0 ttl=64 time=0.056 ms

# 现在可以不用 --link也可以ping名字了
[root@VM-16-5-centos ~]# docker exec tomcat-net-02 ping tomcat-net-01
PING tomcat-net-01 (192.168.0.2): 56 data bytes
64 bytes from 192.168.0.2: icmp_seq=0 ttl=64 time=0.064 ms
64 bytes from 192.168.0.2: icmp_seq=1 ttl=64 time=0.061 ms

我们自定义的docker网络都已经帮我们维护好了对应关系,推荐我们平时这样使用网络！

好处：

redis-不同的集群使用不同的网络,保证我们的集群是安全和健康的

mysql-不同的集群使用不同的网络,保证我们的集群是安全和健康的
```

## 网络连通(连接一个网络到一个容器)

```shell
# 默认docker0网络
[root@VM-16-5-centos ~]# docker run -d -P --name tomcat01 b8
21be63ddeb2f950196998ac5d7fd9604637a1e3a1f06cb7bdc5cbae939e0ef56
[root@VM-16-5-centos ~]# docker run -d -P --name tomcat02 b8
d1ad26ce450d9a4c457a4f6d2322b60dbf2e62f303eea7786c0ea3164814a862
# 测试tomcat01 ping  tomcat-net-02  网段不同 不可能ping通
[root@VM-16-5-centos ~]# docker exec -it tomcat01 ping tomcat-net-02
ping: unknown host

# 打通命令  利用 docker network connect

[root@VM-16-5-centos ~]# docker network --help
Commands:
  connect     Connect a container to a network       # 将容器连接到网络
# 查看connect 命令
[root@VM-16-5-centos ~]# docker network connect --help

Usage:	docker network connect [OPTIONS] NETWORK CONTAINER    # [选项] NETWORK(网络) CONTAINER(容器名)

Connect a container to a network

Options:
      --alias stringSlice           Add network-scoped alias for the container      # 为容器添加网络范围的别名
      --help                        Print usage       # 帮助 打印使用
      --ip string                   IP Address        # ip string ip地址
      --ip6 string                  IPv6 Address      # ip6 string IPv6地址
      --link list                   Add link to another container (default [])     # 添加到另一个容器的链接(默认[])
      --link-local-ip stringSlice   Add a link-local address for the container     # 为容器添加一个链路本地地址 
# ===============================

# 测试打通  tomcat01 到 --mynet
[root@VM-16-5-centos ~]# docker network connect mynet tomcat01

# 连通之后就是将tomcat01防到了mynet网络下
# 一个容器两个ip地址   阿里云服务器  公网ip 私网ip
[root@VM-16-5-centos ~]# docker inspect mynet
[
    {
        "Name": "mynet",
        "Id": "28efdb5a5f68162183fff88bda77888a1c368d3e336919be04b4d4a8b3700ae9",
        "Created": "2022-08-29T20:32:35.707417304+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Containers": {
            "194d832fb4144b2d7871fb236dc3f20213f1609442330975f6d63b3b3a408367": {
                "Name": "tomcat-net-02",
                "EndpointID": "c2d216be4e7b8e75ec56c867b10a5afd3dabb37c6b774c5a69139cea41014f55",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            },
            "21be63ddeb2f950196998ac5d7fd9604637a1e3a1f06cb7bdc5cbae939e0ef56": {
                "Name": "tomcat01",  # 可以查看网络已经连接了进来
                "EndpointID": "b505e683f8b05aa7801b51c92c202d6207ec0391710aa88084a87123500efdf8",
                "MacAddress": "02:42:c0:a8:00:04",
                "IPv4Address": "192.168.0.4/16",
                "IPv6Address": ""
            },
            "f2f49914c83dea308e42839e3ddba928f5b313134286c9742b2558b720c78143": {
                "Name": "tomcat-net-01",
                "EndpointID": "de56c70758fcf122c9612105b76295177a99077070242867e6277aec94e3cea8",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]

# 再次ping网络
[root@VM-16-5-centos ~]# docker exec -it tomcat01 ping tomcat-net-02
PING tomcat-net-02 (192.168.0.3): 56 data bytes
64 bytes from 192.168.0.3: icmp_seq=0 ttl=64 time=0.073 ms
# 02 是依旧打不通的
[root@VM-16-5-centos ~]# docker exec -it tomcat02 ping tomcat-net-02
ping: unknown host

# 结论: 假设要跨网络操作别人,就需要使用docker network connect 连通
```



# [实战-redis集群](https://www.bilibili.com/video/BV1og4y1q7M4?p=38&vd_source=7e2da9cd24687b8e4931e62248cb1ed4)

```shell
# 建立一个网络
[root@VM-16-5-centos ~]# docker network create redis --subnet 172.38.0.0/16
# 查看网络
[root@VM-16-5-centos ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
51354af29e83        bridge              bridge              local
2ff22bd34a38        host                host                local
28efdb5a5f68        mynet               bridge              local
6072e5fef379        none                null                local
301c13c656b0        redis               bridge              local

# 创建配置文件
for port in $(seq 1 6); \
do \
mkdir -p /mydata/redis/node-${port}/conf
touch /mydata/redis/node-${port}/conf/redis.conf
cat << EOF >/mydata/redis/node-${port}/conf/redis.conf
port 6379
bind 0.0.0.0
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.38.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done
#==============================
# 生成6个redis

for port in $(seq 1 6);
do
docker run -p 637${port}:6379 -p 1637${port}:16379 --name redis-${port} \
-v /mydata/redis/node-${port}/data:/data \
-v /mydata/redis/node-${port}/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.1${port} redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
done
#---------------------------
# 查看生成的reids镜像
[root@VM-16-5-centos ~]# docker ps -a
CONTAINER ID        IMAGE                                   COMMAND                  CREATED             STATUS              PORTS                                              NAMES
091ff8eb857d        redis:5.0.9-alpine3.11                  "docker-entrypoint..."   32 seconds ago      Up 31 seconds       0.0.0.0:6376->6379/tcp, 0.0.0.0:16376->16379/tcp   redis-6
7b313776eba8        redis:5.0.9-alpine3.11                  "docker-entrypoint..."   32 seconds ago      Up 31 seconds       0.0.0.0:6375->6379/tcp, 0.0.0.0:16375->16379/tcp   redis-5
3f79c0bfd6c7        redis:5.0.9-alpine3.11                  "docker-entrypoint..."   32 seconds ago      Up 32 seconds       0.0.0.0:6374->6379/tcp, 0.0.0.0:16374->16379/tcp   redis-4
0d6cf0168347        redis:5.0.9-alpine3.11                  "docker-entrypoint..."   33 seconds ago      Up 32 seconds       0.0.0.0:6373->6379/tcp, 0.0.0.0:16373->16379/tcp   redis-3
2457ce22272c        redis:5.0.9-alpine3.11                  "docker-entrypoint..."   33 seconds ago      Up 32 seconds       0.0.0.0:6372->6379/tcp, 0.0.0.0:16372->16379/tcp   redis-2
7feac4765ab9        redis:5.0.9-alpine3.11                  "docker-entrypoint..."   33 seconds ago      Up 33 seconds       0.0.0.0:6371->6379/tcp, 0.0.0.0:16371->16379/tcp   redis-1

# 进入redis-1容器 注意这里是  /bin/sh 不是 /bin/bash

[root@VM-16-5-centos ~]# docker exec -it redis-1 /bin/sh
# 创建集群
/data #   redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379 --cluster-replicas 1

>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 172.38.0.15:6379 to 172.38.0.11:6379
Adding replica 172.38.0.16:6379 to 172.38.0.12:6379
Adding replica 172.38.0.14:6379 to 172.38.0.13:6379
M: 5c68199e6b36b48a3283051d83c350aed51aa95f 172.38.0.11:6379
   slots:[0-5460] (5461 slots) master
M: 09cf68930900681b674f77a7f71eb6b9876133a8 172.38.0.12:6379
   slots:[5461-10922] (5462 slots) master
M: 4715a2f1035077a738dcf762910fe94cb72c0a41 172.38.0.13:6379
   slots:[10923-16383] (5461 slots) master
S: ece06985403256a2356faf0357a76d599a1676f3 172.38.0.14:6379
   replicates 4715a2f1035077a738dcf762910fe94cb72c0a41
S: 32d623677019e2bf98de77b796e15345c2684e5f 172.38.0.15:6379
   replicates 5c68199e6b36b48a3283051d83c350aed51aa95f
S: 1da92edce38c3626da14ce2477651252d9f9aa91 172.38.0.16:6379
   replicates 09cf68930900681b674f77a7f71eb6b9876133a8
Can I set the above configuration? (type 'yes' to accept): yes     # 输入yes配置集群
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
....
>>> Performing Cluster Check (using node 172.38.0.11:6379)
M: 5c68199e6b36b48a3283051d83c350aed51aa95f 172.38.0.11:6379
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: 4715a2f1035077a738dcf762910fe94cb72c0a41 172.38.0.13:6379
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: 1da92edce38c3626da14ce2477651252d9f9aa91 172.38.0.16:6379
   slots: (0 slots) slave
   replicates 09cf68930900681b674f77a7f71eb6b9876133a8
S: 32d623677019e2bf98de77b796e15345c2684e5f 172.38.0.15:6379
   slots: (0 slots) slave
   replicates 5c68199e6b36b48a3283051d83c350aed51aa95f
M: 09cf68930900681b674f77a7f71eb6b9876133a8 172.38.0.12:6379
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: ece06985403256a2356faf0357a76d599a1676f3 172.38.0.14:6379
   slots: (0 slots) slave
   replicates 4715a2f1035077a738dcf762910fe94cb72c0a41
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered. # 配置完毕

#==================================================

# 查看节点   cluster nodes     master主机
172.38.0.13:6379> cluster nodes
1da92edce38c3626da14ce2477651252d9f9aa91 172.38.0.16:6379@16379 slave 09cf68930900681b674f77a7f71eb6b9876133a8 0 1661905351144 6 connected
32d623677019e2bf98de77b796e15345c2684e5f 172.38.0.15:6379@16379 slave 5c68199e6b36b48a3283051d83c350aed51aa95f 0 1661905352146 5 connected
5c68199e6b36b48a3283051d83c350aed51aa95f 172.38.0.11:6379@16379 master - 0 1661905351000 1 connected 0-5460
4715a2f1035077a738dcf762910fe94cb72c0a41 172.38.0.13:6379@16379 myself,master - 0 1661905351000 3 connected 10923-16383
09cf68930900681b674f77a7f71eb6b9876133a8 172.38.0.12:6379@16379 master - 0 1661905350142 2 connected 5461-10922
ece06985403256a2356faf0357a76d599a1676f3 172.38.0.14:6379@16379 slave 4715a2f1035077a738dcf762910fe94cb72c0a41 0 1661905350543 4 connected


# 查看集群 -c 是集群
/data # redis-cli -c
127.0.0.1:6379> cluster info
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:6
cluster_size:3
cluster_current_epoch:6
cluster_my_epoch:1
cluster_stats_messages_ping_sent:188
cluster_stats_messages_pong_sent:172
cluster_stats_messages_sent:360
cluster_stats_messages_ping_received:167
cluster_stats_messages_pong_received:188
cluster_stats_messages_meet_received:5
cluster_stats_messages_received:360
127.0.0.1:6379>
# 设置一个值
127.0.0.1:6379> set a b
-> Redirected to slot [15495] located at 172.38.0.13:6379           # 设置
OK

```







































