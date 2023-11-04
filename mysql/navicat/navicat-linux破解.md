# 	资料

| name     | url                                                          |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://www.cnblogs.com/navysummer/p/16263742.html) [link](https://blog.fengzi.info/?p=288) [link](https://www.cnblogs.com/librarookie/p/15109941.html) [link](https://www.bugquit.com/720.html) |

# 破解navicat

##  官方下载[中文版](http://www.navicat.com.cn/download/navicat-premium)

> wget命令下载,<font color='red'>建议直接网址复制到浏览器请求下载</font> **wget会出现下载失败的情况**

```shell
# 不推荐会下载失败
wget --no-check-certificate http://www.navicat.com.cn/download/direct-download?product=navicat15-premium-cs.AppImage&location=1

# 推荐命令
wget --no-check-certificate https://download.navicat.com.cn/download/navicat15-premium-cs.AppImage
```

## 将下载的镜像设置为可执行文件

```shell
chmod +x navicat15-premium-cs.AppImage
./navicat15-premium-cs.AppImage

# 或则直接鼠标右键 属性-权限-允许执行文件(打沟)
```

**如果启动报错**

![image-20221024091459052](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20221024091459052.png)

**解决方案**

```shell
sudo apt install fuse
```

## [破解](https://www.cnblogs.com/navysummer/p/16263742.html)

# [Linux破解Navicat15](https://www.cnblogs.com/navysummer/p/16263742.html)

**资料**

| name       | url                                                          |
| ---------- | ------------------------------------------------------------ |
| github文档 | [link](https://github.com/dalefeng/navicat-keygen)  [link](https://gitee.com/andisolo/navicat-keygen#/andisolo/navicat-keygen/blob/linux/doc/how-to-build.zh-CN.md) |
| 参考       | [link](https://ylyhappy.gitee.io/posts/linux/install-navciat.html#%E7%A0%B4%E8%A7%A3navicat16-%E4%BD%BF%E7%94%A8-navicat-keygen-for-linux) [link](http://www.catmes.com/archives/deepin-navicat.html)  [link](https://zhuanlan.zhihu.com/p/541577910)  [link](http://www.htsjk.com/shujukunews/45210.html) |

1.下载Navicat15

```shell
wget --no-check-certificate https://download.navicat.com.cn/download/navicat15-premium-cs.AppImage
```

2.文件处理

```shell
navy@DEEPIN:~/Desktop$ mkdir navicat15-premium-cs
navy@DEEPIN:~/Desktop$ sudo mount -o loop navicat15-premium-cs.AppImage  navicat15-premium-cs
navy@DEEPIN:~/Desktop$ cp -r navicat15-premium-cs navicat15-premium-cs-bak
navy@DEEPIN:~/Desktop$ sudo umount navicat15-premium-cs
navy@DEEPIN:~/Desktop$ rm -rf navicat15-premium-cs
```

3.安装依赖

```shell
sudo apt install libcapstone-dev cmake rapidjson-dev  openssl git
```

4.安装keystone

> 安装[官方文档](https://github.com/keystone-engine/keystone/blob/master/docs/COMPILE-NIX.md)

```shell
# 需要安装 gcc  g++ 编译  例如:  apt -y  install gcc g++ 
# 需要安装python  例如  apt -y install python
navy@DEEPIN:~/Desktop$ apt -y  install gcc g++ python cmake
navy@DEEPIN:~/Desktop$ git clone https://github.com/keystone-engine/keystone.git 
#  git clone https://gitee.com/DarrenEpic/keystone-engine.git  keystone                   # 快一点
navy@DEEPIN:~/Desktop/keystone$ cd keystone && mkdir build && cd build
# 注意 需要2G以上内存才可以编译成功不然会一直卡在那
navy@DEEPIN:~/Desktop/keystone/build$ ../make-share.sh
navy@DEEPIN:~/Desktop/keystone/build$  sudo make install
navy@DEEPIN:~/Desktop/keystone/build$ sudo ldconfig
```

5.安装navicat-keygen

> 要先安装**keystone** 这个才可以安装成功

```shell
navy@DEEPIN:~/Desktop$ git clone -b linux --single-branch https://gitee.com/andisolo/navicat-keygen.git
navy@DEEPIN:~/Desktop$ cd navicat-keygen
navy@DEEPIN:~/Desktop/navicat-keygen$ make all


# 如果报错 fatal error: openssl/opensslv.h: No such file or directory
## 参考https://blog.csdn.net/zzddada/article/details/103531771

apt-get install libssl-dev
apt install rapidjson-dev libcapstone-dev
```

6.通过navicat-patcher命令来修改原公钥

```shell
navy@DEEPIN:~/Desktop/navicat-keygen$ ./bin/navicat-patcher ../navicat15-premium-cs-bak/
```

7.[下载打包工具 ](https://yaoliuyang.lanzoul.com/ikINo0katjhg)   or [csdn](https://download.csdn.net/download/leoeitail/11257965)

```shell
navy@DEEPIN:~/Desktop$ wget https://github.com/AppImage/AppImageKit/releases/download/continuous/appimagetool-x86_64.AppImage
navy@DEEPIN:~/Desktop$ chmod +x appimagetool-x86_64.AppImage
```

8.打包成新的app

```shell
navy@DEEPIN:~/Desktop$ ./appimagetool-x86_64.AppImage navicat15-premium-cs-bak/ navicat15-premium-cs-2.AppImage
```

9.运行新的app

```shell
navy@DEEPIN:~/Desktop$ sudo chmod +x ~/Desktop/navicat15-premium-cs-2.AppImage
navy@DEEPIN:~/Desktop$ ./navicat15-premium-cs-2.AppImag
```

点击注册

10.使用 navicat-keygen 来生成序列号和JH码

```shell
navy@DEEPIN:~/Desktop$ cd navicat-keygen
navy@DEEPIN:~/Desktop/navicat-keygen$ ./bin/navicat-keygen --text ./RegPrivateKey.pem
```

>  按提示选择Navicat产品类别（1.Premium）、Navicat语言版本（1.）和填写主版本号（15），随后生成一个序列号，输入用户名和组织，出现Input request code in Base64: (Double press ENTER to end)
> 保留界面，进入第11步。

11.软件界面注册

> 断开网络
>
> 输入上一步生成的Serial number
>
> 点击激活
>
> 提示激活失败,选择手动激活
>
> 复制请求码到第10步
>
> 回车两次生成激活码
>
> 复制navicat-keygen生成的激活码,在软件对话框里的激活码输入框里输入激活码完成激活

 





# 创建桌面图标

>图标可以官网下载**png**格式或者**svg**格式,或者从解压的appimage中寻找对应图标

**桌面创建文件:navicat.desktop**

```shell
# navicat15.desktop
[Desktop Entry]
Type=Application
Name=Navicat Premium 15
GenericName=Database Development Tool
# 指向图标地址
Icon=navicat-icon   
Icon=/home/yaoliuyang/Documents/app/navicat15/navicat-icon.png
# 需要执行的程序路径
Exec=/home/yaoliuyang/Documents/app/navicat15/Navicat_Premium_15_cs-x86_64.appimage
Categories=Development;
Keywords=database;sql;
```

### appimage解压

**资料**

| 名称         | 地址                                                         |
| ------------ | ------------------------------------------------------------ |
| 博客         | [link](https://www.zyku.net/linux/4152.html)                 |
| appimage官网 | [link](https://appimage.org/)  [link](https://www.bilibili.com/read/cv18046728/) |

**操作命令**

```shell
xxx.AppImage --appimage-extract  #例:Navicat_Premium_15_cs-x86_64.appimage    解压出来的图标包含图标&启动文件等
```



#  删除配置文件无限试用

**资料**

| 名称 | 地址                                                         |
| ---- | ------------------------------------------------------------ |
| 博客 | [link](https://www.xmmup.com/linuxubuntuxianavicat-premium-16dewuxianshiyong.html) [link](https://www.cnblogs.com/phpper/p/16668671.html) |

##  操作一

> Navicat Premium 16的试用期只有14天，快到期之前，做如下动作，即可无限使用。
>
> 1、关闭Navicat程序
> 2、删除如下2个文件：

```shell
rm -rf ~/.config/navicat    
rm -rf ~/.config/dconf/user
 
lsof | grep navicat | grep \\.config
# 重新打开即可
```

#  bug解析

##  docker中 mount挂载Navicat_Premium_15_cs 报错 mount: navicat15-premium-cs: mount failed: Operation not permitted.

**资料**

| name     | url                                                          |
| -------- | ------------------------------------------------------------ |
| 参考博客 | [link](https://blog.csdn.net/kunyus/article/details/105531854) |

```shell
# 启动docker时候添加 --privileged=true   超级权限

docker run -itd --name ubuntu_navicat_pj --privileged=true  71c
```

