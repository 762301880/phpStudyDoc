# 	资料

| name     | url                                                        |
| -------- | ---------------------------------------------------------- |
| 网络博客 | [link](https://www.cnblogs.com/navysummer/p/16263742.html) |

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

# 创建桌面图标

>图标可以官网下载**png**格式或者**svg**格式

**桌面创建文件:navicat.desktop**

```shell
# navicat.desktop
[Desktop Entry]
Type=Application
Name=Navicat Premium 15
GenericName=Database Development Tool
Icon=navicat-icon   # 指向图标地址
Exec=navicat       # 需要执行的程序路径
Categories=Development;
Keywords=database;sql;
```

