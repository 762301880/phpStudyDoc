# [ Apifox 接口调试](https://www.apifox.cn/)

![1639119171(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/Yf2bXicVTeu7xQU.png)

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

![image-20231223095517033](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20231223095517033.png)



![image-20231223095545574](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20231223095545574.png)

![image-20231223095651362](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20231223095651362.png)

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

![image-20240102094059757](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240102094059757.png)

##  wsl配置后台启动

> [link](https://zhuanlan.zhihu.com/p/612755941#:~:text=WIN%2BR%20%E8%BF%90%E8%A1%8C%20shell%3Astartup%20%E6%89%93%E5%BC%80%E5%90%AF%E5%8A%A8%E7%9B%AE%E5%BD%95%20%E5%9C%A8%E6%AD%A4%E7%9B%AE%E5%BD%95%E4%B8%AD%E5%88%9B%E5%BB%BA%E6%96%87%E4%BB%B6%20wsl-startup.vbs%20%E5%9C%A8%20wsl-startup.vbs,WSL%20%E7%9A%84%E8%80%97%E6%97%B6%E3%80%82%20%E5%A6%82%E6%9E%9C%E4%BD%A0%E6%8B%85%E5%BF%83%E5%90%8E%E5%8F%B0%E6%8C%82%E7%9D%80WSL%E5%AF%B9%E7%B3%BB%E7%BB%9F%E8%B5%84%E6%BA%90%E5%8D%A0%E7%94%A8%E8%BF%87%E9%AB%98%EF%BC%8C%E5%8F%AF%E4%BB%A5%E9%80%9A%E8%BF%87%E9%85%8D%E7%BD%AE.wslconfig%20%E6%96%87%E4%BB%B6%E6%9D%A5%E9%99%90%E5%88%B6%20WSL%20%E7%9A%84%E8%B5%84%E6%BA%90%E5%8D%A0%E7%94%A8%E3%80%82%20WSL%20%E4%BC%9A%E9%BB%98%E8%AE%A4%E5%8D%A0%E7%94%A850%25%E5%86%85%E5%AD%98%EF%BC%8C%E6%9C%80%E5%A4%A78GB%E3%80%82)

##  bug解析

**wsl不支持systemctl命令**

> https://blog.csdn.net/Ying_ph/article/details/134344910

### WSL2安装Linux子系统报0x80370102解决方法

> https://zhuanlan.zhihu.com/p/391422861

![WSL2安装Linux子系统报0x80370102解决方法](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/v2-f25274f7ab3efe1f63a202a35d9db8a9_1440w.image)

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

![image-20240102115549903](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240102115549903.png)

# windows11

## Windows11右键菜单修改为Windows10风格

> [参考文档](https://blog.csdn.net/WMX_0121/article/details/123231951)

[Windows11](https://so.csdn.net/so/search?q=Windows11&spm=1001.2101.3001.7020)更新后，右键菜单很多功能隐藏起来了，使用时需要点击“显示更多选型”才能获取完整功能。为了能获得Windows10右键菜单丝滑的体验，我总结了以下方法。

![image](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/59704c4ffbd6af9764dd5ab84c0fc7bf.gif)

## 方法一：控制台操作法

- 按住win+R打开运行窗口

输入cmd，打开控制台
在控制泰中输入下方代码，回车，显示操作成功
开启旧版右键菜单：

```shell
reg add "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /f /ve
```

恢复windows11新版右键菜单：

```shell
reg delete "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}" /f
```

- 打开任务管理器，重新启动资源管理器，即完成设置

![image](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/8e65e7a7bd31dbba11cceb33a67235b5.png)



# windows 知识

## windows环境变量是干嘛的

**资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/qq_37967783/article/details/132545609) |

>  Windows环境变量是一种系统范围的配置设置，用于存储操作系统和应用程序所需的信息，以便它们能够正确地运行和交互。这些变量包含了一些路径、地址或者其他系统级别的信息，这些信息对于系统的正常运行和软件的工作至关重要。
>
> 环境变量可以用来存储一些重要的系统路径，比如系统文件夹的位置、应用程序安装路径等等。通过使用环境变量，可以使得系统更加灵活，因为一旦某个路径发生了变化，只需要修改环境变量的值，而不需要逐个修改依赖该路径的所有应用程序。
>
> 例如，PATH环境变量指定了操作系统应该在哪些目录中搜索可执行文件。当你在命令行中输入一个命令时，系统会在PATH指定的目录中查找可执行文件，从而执行相应的操作。这使得你可以在任何目录下直接运行命令，而不必输入完整的路径。
>
> 总之，Windows环境变量为操作系统和应用程序提供了一种灵活、可配置的方式来获取和使用系统级别的信息和设置。

# [Windows Terminal _windows10全新命令行工具](https://www.cnblogs.com/yaoliuyang/p/12969627.html)

[安装链接](https://github.com/microsoft/terminal/releases)



# dll修复工具

**参考资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/Cybertronnnnnn/article/details/140595309) |

## [dll-files网站修复](https://www.dll-files.com/)

> 直接搜索缺失得dll文件

#  手机投屏电脑

##  Genymobile/scrcpy

**资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| github源码 | [link](https://github.com/Genymobile/scrcpy?tab=readme-ov-file) |
| 网络博客   | [link](https://blog.csdn.net/was172/article/details/99705855) |

> **注意手机需要开启  USB调试**

**使用**

```shell
adb.exe devices
scrcpy.exe --video-codec=h265 --max-size=800 --max-fps=60 --no-audio --window-title='phone'
```

**参数介绍**

> --window-title          默认情况下，窗口标题是设备型号。可以更改
>
> --always-on-top      要使窗口始终位于最前面：
>
>  --no-audio            禁用音频
>
>  --max-size=1920   将尺寸限制为 1920
>
> --video-codec=h265   （质量更好）捕获屏幕
>
> --max-fps=60   将帧速率限制为 60fps

# 电脑硬件检测工具

> 鲁大师需要下载

推荐使用腾讯电脑关键-工具箱-硬件检测

# 桌面整理

## 腾讯桌面整理(强烈推荐)