# 千万不要删除自带的python

## 说明

> 事情是这样的,由于deepin系统自带了python2.7，由于现在python
>
> 都出3了想着自己升级一下于是卸载了系统自带的python2（此处使用
>
> 了**apt autoremove python** 命令），好嘛想着上次deepin系统因为不知道
>
> 做了什么操作导致系统桌面丢失，于是想着重启一下果然系统桌面又
>
> 丢失了,于是百度了一番deepin系统果然有删除自带python桌面丢失的现象

## 解决方案

**参考资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://blog.csdn.net/hulang_better_me/article/details/99620350)  [link](https://blog.csdn.net/Brilliant_orange/article/details/110517149?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-2.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-2.no_search_link) |
|            |                                                              |

**依次安装**

```shell
# 更新软件列表
sudo apt-get update 
# 安装更新并	识别出当依赖
sudo apt-get dist-upgrade 
# 安装Deepin桌面环境  https://www.jianshu.com/p/5347188c00f3
sudo apt-get install dde
# 从新启动
reboot 
```

