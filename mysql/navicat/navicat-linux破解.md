# 	资料

| name     | url                                                          |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://www.cnblogs.com/navysummer/p/16263742.html) [link](https://blog.fengzi.info/?p=288) [link](https://www.cnblogs.com/librarookie/p/15109941.html) |

# 破解navicat

##  官方下载[中文版](http://www.navicat.com.cn/download/navicat-premium)

> wget命令下载,<font color='red'>建议直接网址复制到浏览器请求下载</font> **wget会出现下载失败的情况**

```shell
# 不推荐会下载失败
wget http://www.navicat.com.cn/download/direct-download?product=navicat15-premium-cs.AppImage&location=1

# 推荐命令
wget https://download.navicat.com.cn/download/navicat15-premium-cs.AppImage
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

