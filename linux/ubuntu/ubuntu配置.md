

# [修改软件源](https://blog.csdn.net/aodechudawei/article/details/122433257)

> 我们在下载软件的时候可能会遇到**无法定位软件包 sshd**这种情况,需要修改默认的软件源,点击左下角的**显示应用程序**,点击**软件和更新**

[![xtZuM8.png](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/xtZuM8.png)](https://imgse.com/i/xtZuM8)



## 服务端修改软件源

| 名称         | 地址                                                         |
| ------------ | ------------------------------------------------------------ |
| 阿里云镜像站 | [link](https://developer.aliyun.com/mirror/ubuntu?spm=a2c6h.13651102.0.0.3e221b11FT0fG7)  [link](https://developer.aliyun.com/mirror/ubuntu-ports?spm=a2c6h.13651104.d-1008.9.12504763nIk3RZ) |

**查看版本**

> 这个命令会显示包括Ubuntu版本号在内的系统信息。如果是22.04 LTS (Jammy Jellyfish)，则会直接在输出中看到"Jammy"字样。 
>
> 2. **检查系统设置**：点击系统设置 -> 关于，这里会展示您的Ubuntu版本信息。如果是22.04 LTS，那么它就是Jammy版本。 
> 3.  **官方发布信息查询**：访问Ubuntu的官方网站或者关注其社区更新，了解最新的发布信息。例如，Ubuntu 22.04 LTS在发布时被命名为"Jammy Jellyfish"。
> 4.  **软件包管理器查询**：使用`apt`或`apt-get`等软件包管理工具搜索和安装软件时，通常会显示系统版本信息，也可以从中确认当前系统是否为Jammy版本。
> 5.  **查看启动画面**：当您启动计算机时，Ubuntu的启动画面通常会显示版本信息。
> 6.  **终端欢迎消息**：每次打开新的终端窗口时，系统都会显示一个欢迎消息，其中包含版本信息。 
> 7.  **查看/etc/os-release文件**：可以使用文本编辑器或命令行工具查看`/etc/os-release`文件中的内容，该文件包含了操作系统的详细版本信息。

```shell
yly@ubuntu:/etc/apt$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 22.04.4 LTS
Release:	22.04
Codename:	jammy
```

###  **ubuntu 20.04** 镜像源

```shell
deb http://archive.ubuntu.com/ubuntu focal main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu focal main restricted universe multiverse

deb http://security.ubuntu.com/ubuntu focal-security main restricted universe multiverse
deb-src http://security.ubuntu.com/ubuntu focal-security main restricted universe multiverse

deb http://archive.ubuntu.com/ubuntu focal-updates main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu focal-updates main restricted universe multiverse

deb http://archive.ubuntu.com/ubuntu focal-backports main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu focal-backports main restricted universe multiverse
```



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



