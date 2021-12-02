[^author:yaoliuyang]

[^created_at:2021/10/28]

[^last_update_at:2021/10/28]

# 资料

## 说明

> 本着搞科研的目，想着本地如果能使用xshell连接本地的centos岂不是以后
>
> 都有了一台免费的服务器而且可以想怎么折腾就怎么折腾

## 资料

| 名称           | 地址                                                         |
| -------------- | ------------------------------------------------------------ |
| 第三方博客参考 | [link](https://cloud.tencent.com/developer/article/1372999)  |
| 参考博客       | [link](https://blog.csdn.net/qq_34940644/article/details/107553041) |

# 操作示例

## 通过下载docker容器下载

> 推荐下载之前先修改镜像容器的镜像源为阿里云源：https://developer.aliyun.com/mirror/centos?spm=a2c6h.13651102.0.0.3e221b119ri5ao

**下载centos镜像**

```shell
# 下载最新的centos镜像
PS C:\Users\Administrator> docker pull centos:latest         
# 查看下载的镜像
PS C:\Users\Administrator> docker images                     
REPOSITORY      TAG                       IMAGE ID       CREATED       SIZE
centos          latest                    5d0da3dc9764   6 weeks ago   231MB
# 构建镜像为容器
PS C:\Users\Administrator> docker run -itd --name local_link_xshell_centos -p 6666:22 5d /usr/sbin/init
11fe3d74f2cd16ee93679b010b02a21f9f28f39edadde69f70e0e329d078da69
# 查看启动的容器
PS C:\Users\Administrator> docker ps -a
CONTAINER ID   IMAGE                                   COMMAND       CREATED          STATUS                      PORTS                                       NAMES
11fe3d74f2cd   5d                                      "/bin/bash"   31 seconds ago   Up 30 seconds               0.0.0.0:6666->22/tcp, :::6666->22/tcp       local_link_xshell_centos
# 进入启动的容器-进入centos容器内部
PS C:\Users\Administrator> docker exec -it 11f /bin/bash
[root@11fe3d74f2cd /]#
# 安装passwd
[root@11fe3d74f2cd /]# yum -y install passwd
# 修改root用户的密码，菜鸟教程passwd命令 https://www.runoob.com/linux/linux-comm-passwd.html
[root@11fe3d74f2cd /]# passwd root
Changing password for user root.
New password:  # 这里输入至少8位密码
BAD PASSWORD: The password fails the dictionary check - it is too simplistic/systematic
Retype new password:  # 重复密码
passwd: all authentication tokens updated successfully.
# 安装ssh 可以研究一下这个 https://www.runoob.com/linux/linux-remote-login.html
[root@11fe3d74f2cd /]#  yum -y install openssh-server 
# 安装网络工具
[root@101fe8e80378 /]#  yum -y install net-tools.x86_64 
# 启动ssh
[root@101fe8e80378 /]# systemctl start sshd
```

## Xshell连接

**创建连接**

![1635411964(1).jpg](https://i.loli.net/2021/10/28/1de6CLnsoMG84Ew.png)

**连接后的界面**

> 直接输入用用户名**root**&设置修改的密码即可连接

![1635412093(1).png](https://i.loli.net/2021/10/28/H1mCvPOQa2b3ewz.png)

