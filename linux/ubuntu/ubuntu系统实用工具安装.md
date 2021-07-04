# 一、`Ubuntu`安装百度输入法

> 由于将自己的笔记本改成了Ubuntu版本发现系统自带的输入法不灵活
>
> 所以使用第三方的输入法

 ## 1.1  下载百度输入法linxu版本



# 二、`Ubuntu`系统安装`wps`

## 2.1 资料

| 项目名称                | 项目地址                                                     |
| ----------------------- | ------------------------------------------------------------ |
| `WPS2019-linux`版本地址 | [链接](https://linux.wps.cn/#)                               |
| `deb`wps格式地址        | [链接](https://wdl1.cache.wps.cn/wps/download/ep/Linux2019/10161/wps-office_11.1.0.10161_amd64.deb) |

## 2.2 下载`wps`

> 使用`wget`命令下载网络包到本地

```apl
sudo wget https://wdl1.cache.wps.cn/wps/download/ep/Linux2019/10161/wps-office_11.1.0.10161_amd64.deb
```

## 2.3 安装`wps`

> 使用`dpkg`命令安装下载的deb包即可

```api
sudo dpkg -i  wps-office_11.1.0.10161_amd64.deb
```

# 三、`ubuntu`安装`qq`音乐

## 3.1官网下载`qq音乐deb`安装包官网

- 官网地址

| 名称         | 链接                                                         |
| ------------ | ------------------------------------------------------------ |
| 官网下载地址 | [链接](https://y.qq.com/download/download.html)              |
| deb下载链接  | [链接](https://dldir1.qq.com/music/clntupate/linux/deb/qqmusic_1.0.9_amd64.deb) |

## 3.2安装qq音乐

```shell
sudo dpkg -i qqmusic_1.0.9_amd64.deb
```

## 3.3 卸载qq音乐

```shell
sudo dpkg -r qqmusic
```

# 四 、Ubuntu系统安装谷歌浏览器

- 官网地址

| 官网下载地址  | 地址                                  |
| ------------- | ------------------------------------- |
| Google Chrome | [链接](https://www.google.cn/chrome/) |

## 4.1  从网络下载deb包

```shell
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
```

## 4.2 安装

```shell
sudo dpkg -i  google-chrome-stable_current_amd64.deb
```

