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
>      　　1. 找到VirtualBox安装位置，进入其中，找到drivers/network/netlwf文件夹下，有一个文件VBoxNetLwf.inf
>      　　2. 打开网络适配器，找到虚拟机的网络连接，打开属性，分别点击安装-服务-添加-从磁盘安装-浏览
>      　　3. 找到1中的VBoxNetLwf.inf文件，安装
>      　　4. 打开VM VirtualBox，设置-网络，选择桥接网卡，此时出现可选项，选择与主机同一个名称的网络即可

![image-20240106105047113](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240106105047113.png)

> **安装完成虚拟机桥接网络出现名称选项**

![image-20240106105131402](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240106105131402.png)

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

## 虚拟机网络模式

### 桥接模式(不用映射端口访问项目)

> 例如你的虚拟机ip地址是**10.198.9.201**  使用了桥接模式 就可以直接通过ip地址访问虚拟机项目

桥接模式（桥接适配器）通俗解释

一句话：**把虚拟机当成一台独立的、和你电脑并列的另一台电脑，直接接入你家里 / 办公室的局域网**。

对比 NAT（你现在用的模式）和桥接模式

1）NAT（当前）

你的电脑 = 路由器 虚拟机藏在电脑内部一个独立小子网（`10.0.2.x`）

- 虚拟机可以访问外网
- **外面（宿主机、手机）不能直接访问虚拟机**
- 每开一个端口，就要单独做端口转发，端口多了非常麻烦

> 类比：你家里的路由器下面再接一个小路由器，小路由器里面的设备外面不能直接访问。

2）桥接模式 Bridge

虚拟机**直接连接到你电脑正在用的路由器**，和宿主机平级。

- 你的电脑 IP：`192.168.1.100`
- 虚拟机 IP：`192.168.1.105`（同一个网段！）
- ✅ 宿主机可以直接访问虚拟机所有端口 `192.168.1.105:1997`
- ✅ 同一个 WiFi 下的手机也能访问这个 IP
- ✅ **不需要任何端口转发**，docker 映射多少端口都直接可用，就像 VMware 桥接

> 类比：路由器直接插了两根网线，一根连你电脑，一根连虚拟机，两台设备是平等的。

工作原理

VirtualBox 虚拟网卡会**转发二层网络数据包**，虚拟机发出的数据包直接送到真实路由器，由路由器分配局域网 IP。

> 路由器会认为虚拟机是局域网里一台真实物理设备，单独给它分配 IP。

## 虚拟机如何挂后台

VirtualBox 后台运行（Headless 无界面模式）

图形界面直接后台启动（最简单）

1. 在 VirtualBox 管理器选中虚拟机
2. 点【启动】旁边下拉箭头 → **Headless Start（无界面启动）**Oracle Vir...

> 快捷键：按住 `Shift` 双击虚拟机，直接后台启动

1. 启动后虚拟机在后台跑，**VirtualBox 管理器主窗口可以关掉，虚拟机继续运行**
2. 需要调出窗口：选中虚拟机，点上方【显示】，随时调出图形界面

> 已经打开虚拟机窗口后转后台： 虚拟机窗口菜单【机器】→【分离 GUI Detach GUI】，窗口关闭，虚拟机后台继续运行；或者直接点窗口关闭按钮，弹窗选择**继续在后台运行**Oracle Vir...