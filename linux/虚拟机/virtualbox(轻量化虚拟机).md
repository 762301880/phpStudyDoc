## virtualbox(轻量化虚拟机)

## [官方网站](https://www.virtualbox.org/)



## [虚拟机安装deepin](https://blog.csdn.net/Crush_Y_L/article/details/129953891)

## bug解析

###   本地文件无法拖放到容器内部解决

![image-20231128145155210](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20231128145155210.png)

**设置拖放指向为双向**

![image-20231128145248598](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20231128145248598.png)

**安装增强功能**

> 点击之后会在容器内部的主机内显示出一个文件夹

![image-20231128145352968](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20231128145352968.png)

**执行脚本安装**

![image-20231128145532897](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20231128145532897.png)

**挂载盘记得设置为**

![image-20231129105901242](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20231129105901242.png)

**安装完成后记得重启虚拟机**

### 安装完成之后重启一直重复安装

**记得deepin设置存储-删除绑定的镜像文件**

## 虚拟机安装ubuntu

**点击新建-选择对应的安装文件目录镜像且跳过自动检测**

![image-20231129084759603](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20231129084759603.png)

##  虚拟机安装alpine

**资料**

| name     | url                                                          |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/WHQ78164/article/details/113850672) |

> <font color='red'>只有一个要求 要是执行持久化 虚拟机需要移除虚拟盘才可以持久化成功不然一直都是最新的</font>

![image-20240106100126752](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240106100126752.png)

###  bug解析

#### [VM VirtualBox 无法连接桥接网络](https://www.cnblogs.com/lcy-qzzl/p/12620973.html)

> https://blog.csdn.net/victorwjw/article/details/122524287
>
> 问题：
>
> 　　在Windows系统下，安装了VM VirtualBox，并创建虚拟机，如Linux(CentOS-7)，创建虚拟机后网络设置中，桥接网络没有选项
>
> 解决：
>
>   　　1. 找到VirtualBox安装位置，进入其中，找到drivers/network/netlwf文件夹下，有一个文件VBoxNetLwf.inf
>
>     　　2. 打开网络适配器，找到虚拟机的网络连接，打开属性，分别点击安装-服务-添加-从磁盘安装-浏览
>
>       　　3. 找到1中的VBoxNetLwf.inf文件，安装
>
>         　　4. 打开VM VirtualBox，设置-网络，选择桥接网卡，此时出现可选项，选择与主机同一个名称的网络即可

![image-20240106105047113](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240106105047113.png)

> **安装完成虚拟机桥接网络出现名称选项**

![image-20240106105131402](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240106105131402.png)

## wget: unable to resolve host address亲测有效办法 & ip addr  无法看见 ens33 的ip地址  & 虚拟机ping不通宿主机与外网

> [参考博客](https://blog.csdn.net/qq_40408065/article/details/117958147)

**解决方案**

> 现象：
> 我的虚拟机使用连接方式的是nat，
> 宿主机能ping通Vmnet8，虚拟机ip，
> 但是虚拟机（centos7）只能ping通VMnet8自己的网关，宿主机与外网ping不通。
>
> 百度csdn等等找了很多；
> 什么在 vi /etc/reslov.conf里面修改dns=8.8.8.8为谷歌的，
> 在网络配置里面设置静态ip等等，没有一个能够使用的；
> 桥接模式也试过总是会出现问题；
>
> 解决方法：虚拟机选择编辑---------虚拟网络编辑--------选择NAT模式
>
> 之后重新刷新网络解决不能wget与ping不通外网的问题！

![在这里插入图片描述](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/20210616145616256.png)

## 虚拟机文件夹挂载

**设置→共享文件夹**



![](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20260916160842369.png)

虚拟机内手动挂载命令（保存好，每次开机要执行）

```bash
# 先创建 /www 目录
mkdir -p /www
# WWW 对应上面【文件夹名称】
mount -t vboxsf WWW /www
```

