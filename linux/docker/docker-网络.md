# Docker 网络

## 理解docker网络

>三个网络

```shell
[root@VM-1-64-centos ~]# ip addr
# 本机回环地址
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
# 腾讯云内网地址
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 52:54:00:17:c3:c1 brd ff:ff:ff:ff:ff:ff
    inet 10.186.1.64/16 brd 10.186.255.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::5054:ff:fe17:c3c1/64 scope link 
       valid_lft forever preferred_lft forever
# docker0  地址
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:7d:f1:d6:60 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 scope global docker0
       valid_lft forever preferred_lft forever
```

## 测试

```php
[root@VM-1-64-centos ~]# docker run -d -P --name tomcat01  tomcat

#查看容器内部网络地址 ip addr

```

# 待补充