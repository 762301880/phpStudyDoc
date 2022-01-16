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

















