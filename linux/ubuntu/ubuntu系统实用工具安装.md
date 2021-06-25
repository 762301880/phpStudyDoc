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

