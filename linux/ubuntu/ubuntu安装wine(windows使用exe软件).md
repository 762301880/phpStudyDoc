**资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| wine官网 | [link](https://www.winehq.org/)   [ubuntu_安装wine](https://wiki.winehq.org/Ubuntu_zhcn) |

# **安装**

## **1. <font color='red'>如果您使用的是 64 位系统，请开启 32 bit 架构支持（如果您之前没有开启的话）：</font>**

```shell
sudo dpkg --add-architecture i386 
```

## 2.下载添加仓库密钥：

```shell
sudo wget -nc -O /usr/share/keyrings/winehq-archive.key https://dl.winehq.org/wine-builds/winehq.key
```

## 3.添加仓库

| For this version(这个版本): |                 Use this command(使用命令):                  |
| :-------------------------: | :----------------------------------------------------------: |
|        Ubuntu 22.04         | `sudo wget -NP /etc/apt/sources.list.d/ https://dl.winehq.org/wine-builds/ubuntu/dists/jammy/winehq-jammy.sources ` |
| Ubuntu 20.04Linux Mint 20.x | `sudo wget -NP /etc/apt/sources.list.d/ https://dl.winehq.org/wine-builds/ubuntu/dists/focal/winehq-focal.sources ` |
| Ubuntu 18.04Linux Mint 19.x | `sudo wget -NP /etc/apt/sources.list.d/ https://dl.winehq.org/wine-builds/ubuntu/dists/bionic/winehq-bionic.sources` |

**更新安装包**

```shell
sudo apt update
```

## 4. 然后安装 **以下任一一个安装包**：

|   稳定分支   | `sudo apt install --install-recommends winehq-stable ` |
| :----------: | ------------------------------------------------------ |
|   开发分支   | `sudo apt install --install-recommends winehq-devel `  |
| Staging 分支 | `sudo apt install --install-recommends winehq-staging` |

**<font color='red'>如果 apt-get 提示缺少依赖，请先安装缺少的依赖，然后重复以上两步（update 和 install）。更多故障处理技巧请参考 [the FAQ entry on dependency errors](https://wiki.winehq.org/FAQ#How_do_I_solve_dependency_errors_when_trying_to_install_Wine.3F)。</font>**

## apt 直接安装

```shell
# 搜索 wine64
sudo apt search wine
# 安装
sudo apt install wine64-tools
```



# 使用

```shell
# 安装exe软件
wine  [exe软件路径]
```



#  bug解析

## [更新软件出现  release文件已经过期。该仓库更新将不会应用](https://blog.csdn.net/weixin_45461706/article/details/124357295)

> 问题复现服务器**时区存在问题**请修改时区

[![示例](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/xYIc0s.jpg)





