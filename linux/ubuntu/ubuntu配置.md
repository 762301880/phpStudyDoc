# [修改软件源](https://blog.csdn.net/aodechudawei/article/details/122433257)

> 我们在下载软件的时候可能会遇到**无法定位软件包 sshd**这种情况,需要修改默认的软件源,点击左下角的**显示应用程序**,点击**软件和更新**

[![xtZuM8.png](https://s1.ax1x.com/2022/10/10/xtZuM8.png)](https://imgse.com/i/xtZuM8)

#  配置虚拟域名

## 切换root用户

```shell
sudo su root 
```

##  进入hosts目录&编辑hosts

- 打开hosts目录

```shell
cd /etc/
vim hosts
```

- 添加虚拟域名

```shell
127.0.0.1	localhost
127.0.1.1	yaoliuyang-Lenovo-XiaoXin-310-15IKB
127.0.0.1   www.cs.com # 自己添加的虚拟域名

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

- 推出保存编辑

> 英文模式下按`Esc`键 在下方的：中输入`wq`(保存并退出)



