# 	一、资料

| name                                                         | url                          |
| ------------------------------------------------------------ | ---------------------------- |
| NavicatForLinux免费在线破解(在线-记得需要下载网站提供的软件)-激活后打包上传网盘下次可以直接使用(亲测) | [免费破解](https://rlds.tk/) |
|                                                              |                              |
|                                                              |                              |

# 由于第一种情况已经满足我的需求了，如果日后有需要再补充第二种情况

[请下载上述官网提供的navicat 版本不然无法破解](https://rlds.tk/down.php)

# 二、破解navicat(勿看没用了)

## 2.1 官方下载[中文版](http://www.navicat.com.cn/download/navicat-premium)

> wget命令下载

```shell
wget http://www.navicat.com.cn/download/direct-download?product=navicat15-premium-cs.AppImage&location=1
```

### 2.2 将下载的镜像设置为可执行文件

```shell
chmod +x navicat15-premium-cs.AppImage
./navicat15-premium-cs.AppImage
```

- 或则直接鼠标右键 属性-权限-允许执行文件(打沟)



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

