# [Apifox 接口调试](https://www.apifox.cn/)

![1639119171(1).jpg](https://s2.loli.net/2021/12/10/Yf2bXicVTeu7xQU.png)

# [火绒电脑管家](https://www.huorong.cn/)(必装)

> 为什么说这个是必须要装的,上次没事从网上下载了一个分区软件刚打开坏了一看安装下面一行全是一行小字让
>
> 安装第三方的软件,好家伙我关了也没用,周日晚上远程连接电脑想着查点资料,直接电脑上全是垃圾软件,卸载了
>
> 一轮之后重启又冒出很多，腾讯电脑管家杀毒了也没用,无奈**http://piaoyi.org/virus/clear-Adware-Gsui.a.html**
>
> 从这里看到了火绒杀毒，下载之后查杀60多个病毒。杀毒然后禁用一些进程之后果然重启没有再自动安装软件了

#  bat命令大全

| 名称                                     | 地址                                                    |
| ---------------------------------------- | ------------------------------------------------------- |
| 书栈网 ***Windows 批处理(bat)语法大全*** | [link](https://www.bookstack.cn/books/zhaoqingqing-bat) |

# windows破解 播HEVC视频收费

| 名称                          | 地址                                           |
| ----------------------------- | ---------------------------------------------- |
| 网络博客                      | [link](https://zhuanlan.zhihu.com/p/354048233) |
| Microsoft商店的在线链接生成器 | [link](https://store.rg-adguard.net/)          |

## **破解下载**

> https://www.free-codecs.com/guides/how_to_download_hevc_video_extension_for_free.htm
>
> 在**[Microsoft商店的在线链接生成器](https://store.rg-adguard.net/)**中输入以下链接并安装

```shell
https://www.microsoft.com/en-us/p/hevc-video-extensions-from-device-manufacturer/9n4wgh0z6vhq
```

[**个人网盘下载**](https://yaoliuyang.lanzoul.com/iW4zz1ipo45i)

#  google play在线下载

> 众所周知我们想下载谷歌商店应用的情况下必须要下载谷歌商店的软件非常麻烦有没有能直接在网页下载的
>
> 办法呢

| 名称              | 地址                                                         |
| ----------------- | ------------------------------------------------------------ |
| google play(官网) | [link](https://play.google.com/store/games)                  |
| 第三方软件下载站  | [link-墙](https://apkcombo.com/downloader/)  [link-墙](https://apk.support/apk-downloader) |

## **操作步骤**

![image-20231223095517033](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20231223095517033.png)



![image-20231223095545574](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20231223095545574.png)

![image-20231223095651362](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20231223095651362.png)

# windows10-wsl安装

**资料**

| name            | url                                                          |
| --------------- | ------------------------------------------------------------ |
| wsl安装官方文档 | [link](https://learn.microsoft.com/zh-cn/windows/wsl/install) |



## 安装wsl

> https://blog.csdn.net/q20010619/article/details/120660346

```shell
# 更新 wsl
wsl --update
## 将 wsl 版本设置为 wsl2
wsl --set-default-version 2
## 执行安装
wsl --install 
```



##  xshell连接wsl

**资料**

| name         | url                                                       |
| ------------ | --------------------------------------------------------- |
| 网络博客参考 | [link](https://www.cnblogs.com/taylorshi/p/13698345.html) |



```shell
# 进入wsl  输入命令 wsl
PS C:\Users\铺先生技术研发中心> wsl
yaoliuyang@DESKTOP-GHQ23UN:/mnt/c/Users/铺先生技术研发中心$

# 安装openssh-server
sudo apt update
sudo apt-get remove --purge openssh-server   ## 先删ssh
sudo apt-get install openssh-server          ## 在安装ssh  
sudo rm /etc/ssh/ssh_config                  ## 删配置文件，让ssh服务自己想办法链接
sudo service ssh --full-restart



# 编辑ssh配置文件
vim /etc/ssh/sshd_config

# 添加配置

Port 22   # 代表启用端口号22
ListenAddress 0.0.0.0   # 代表监听所有地址
PasswordAuthentication yes  # yes,代表支持账号密码形式的登录

# 设置后 重启SSH服务
sudo service ssh restart
```

**xshell连接**

![image-20240102094059757](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20240102094059757.png)

##  bug解析

**wsl不支持systemctl命令**

> https://blog.csdn.net/Ying_ph/article/details/134344910

### WSL2安装Linux子系统报0x80370102解决方法

> https://zhuanlan.zhihu.com/p/391422861

![WSL2安装Linux子系统报0x80370102解决方法](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/v2-f25274f7ab3efe1f63a202a35d9db8a9_1440w.image)

解决办法也简单：

以管理员权限打开cmd，输入一下命令，然后重启，

```shell
bcdedit /set hypervisorlaunchtype auto
```

此命令的作用是在让Win10启动时，加载Hyper-V的必要程序资源。

重启后若Hyper-v能正常运行，那么WSL2差不多也能了。

题外话：此命令执行后，VMware就不能很好兼容了，鱼和熊掌不可兼得，若想完美使用VMware，管理员执行以下命令关闭即可。

```shell
bcdedit /set hypervisorlaunchtype off
```

我的Hyper-v启动不了又是另一个情况，我是不知道什么时候手贱把services.msc里面有关Hyper-v的功能禁用了几个，取消禁用，设置为自动或手动即可。让我白白浪费了好几晚上。。。

# windows-Desktop转移到D盘

> https://www.haozhuangji.com/xtjc/133515871.html
>
> **在D盘下创建 Desktop文件夹 然后将桌面文件移动到D盘即可**

![image-20240102115549903](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20240102115549903.png)