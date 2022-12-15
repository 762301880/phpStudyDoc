

# 使用 ntpdate 命令同步

## 资料

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/qq_39715000/article/details/120730694) |

## 操作步骤

```shell
# 简化步骤
sudo apt install ntpdate
sudo ntpdate asia.pool.ntp.org


#-----------------具体步骤-----------------------
# 使用date命令可以查看当前日期今天实际日期为:      2022年 12月 15日 星期四 14:58:31 CST
yaoliuyang@yly-ununtu:~$ date
2022年 11月 30日 星期三 10:23:03 CST
# 使用 ntpdate 命令同步日期

yaoliuyang@yly-ununtu:~$ sudo ntpdate asia.pool.ntp.org
sudo: ntpdate：找不到命令
# 安装ntpdate
yaoliuyang@yly-ununtu:~$ sudo apt install ntpdate
正在读取软件包列表... 完成
正在分析软件包的依赖关系树... 完成
正在读取状态信息... 完成                 
下列软件包是自动安装的并且现在不需要了：
  libflashrom1 libftdi1-2 libfuse2 libntfs-3g89
使用'sudo apt autoremove'来卸载它(它们)。
下列【新】软件包将被安装：
  ntpdate
升级了 0 个软件包，新安装了 1 个软件包，要卸载 0 个软件包，有 97 个软件包未被升级。
需要下载 51.5 kB 的归档。
解压缩后会消耗 178 kB 的额外空间。
获取:1 https://repo.huaweicloud.com/ubuntu jammy/universe amd64 ntpdate amd64 1:4.2.8p15+dfsg-1ubuntu2 [51.5 kB]
已下载 51.5 kB，耗时 0秒 (359 kB/s)
正在选中未选择的软件包 ntpdate。
(正在读取数据库 ... 系统当前共安装有 211092 个文件和目录。)
准备解压 .../ntpdate_1%3a4.2.8p15+dfsg-1ubuntu2_amd64.deb  ...
正在解压 ntpdate (1:4.2.8p15+dfsg-1ubuntu2) ...
正在设置 ntpdate (1:4.2.8p15+dfsg-1ubuntu2) ...
正在处理用于 man-db (2.10.2-1) 的触发器 ...
# 再次同步
yaoliuyang@yly-ununtu:~$ sudo ntpdate asia.pool.ntp.org
15 Dec 14:58:29 ntpdate[7983]: step time server 110.170.126.105 offset +1312476.310126 sec
yaoliuyang@yly-ununtu:~$ date
2022年 12月 15日 星期四 14:58:31 CST

```

