## 虚拟机中使用ubuntu桌面右键菜单不全

> 如下图所示桌面图标消失切右键显示**菜单丢失**

[![xbG5Bn.png](https://s1.ax1x.com/2022/11/03/xbG5Bn.png)](https://imgse.com/i/xbG5Bn)

**解决方案**

| 参考资料 | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/linyiheng666/article/details/126265108) |

> 如果[ubuntu](https://so.csdn.net/so/search?q=ubuntu&spm=1001.2101.3001.7020)的桌面上所有文件突然消失，但是通过终端命令ls查看桌面仍存在文件，同时在桌面右键后的菜单也显示不全，则可以通过执行下面命令来恢复

```shell
sudo apt install -f
sudo aptt update
sudo apt  upgrade
sudo apt  install ubuntu-desktop
reboot
```

