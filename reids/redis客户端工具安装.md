# 一、资料

## 1.1地址

| 名称                         | 地址                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| 说明地址                     | [链接](https://mp.weixin.qq.com/s/S05rgr5JPZSOWJmOvCIzfg)    |
| 软件下载地址   gitee下载地址 | [链接](https://github.com/qishibo/AnotherRedisDesktopManager/releases)   [链接](https://gitee.com/qishibo/AnotherRedisDesktopManager/releases) |
| gitee地址                    | [链接](https://gitee.com/qishibo/AnotherRedisDesktopManager) |

# 二、下载

## 2.1请打开github地址

```bash
https://github.com/qishibo/AnotherRedisDesktopManager/releases
```

## 2.2 下载windows或则mac版本

- 由于github下载比较慢我这里提供了网盘下载

| name                  | url                                        |
| --------------------- | ------------------------------------------ |
| 蓝奏云（windows版本） | https://yaoliuyang.lanzoui.com/iijgcpw3yva |
| 蓝奏云（mac版本）     | https://yaoliuyang.lanzoui.com/iOA9Gpwbeyb |

![image-20210607095046001](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20210607095046001.png)

## 2.3 linux版本下载

[下载地址](https://gitee.com/qishibo/AnotherRedisDesktopManager/releases)

```shell
wget https://gitee.com/qishibo/AnotherRedisDesktopManager/attach_files/893990/download/eRedis-Desktop-Manager.1.5.0.AppImage
# 执行命令-设置可执行文权限
chmod + x  下载的文件
```

### 设置桌面图标

> 设置桌面文件**Another_Redis.desktop** 并修改**Exec&Icon**指向

```shell
# Another_Redis.desktop 
[Desktop Entry]
Name=Another-Redis
Comment=redis app
GenericName=Another-Redis
Exec=/home/yaoliuyang/Documents/app/redis_app/Another-Redis-Desktop-Manager.1.5.8.AppImage
Icon=/home/yaoliuyang/Documents/app/redis_app/Another Redis Desktop Manager.png
Type=Application
StartupNotify=true
Categories=Office;WordProcessor;
MimeType=text/markdown;text/x-markdown;
X-Deepin-CreatedBy=com.deepin.dde.daemon.Launcher
X-Deepin-AppID=another_redis
```

**补充**

**如果没有图标文件**

> 更多资料参考  https://www.cnblogs.com/sinferwu/p/16827735.html

```shell
# 解压.AppImage文件
./Another-Redis-Desktop-Manager.1.5.9.AppImage --appimage-extract
```



# 三、使用

- 双击已经安装的`Another Redis Desktop Manager `

## 3.1 基本设置

- 修改语言

  ![image-20210607134157860](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20210607134157860.png)

- 本地连接

![image-20210607134744191](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20210607134744191.png)

以上设置完毕直接在项目中使用即可

