# 镜像地址

| 名称                                                         | 地址                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| alpine官网-官网下载贼慢不推荐                                | [link](https://www.alpinelinux.org/downloads/)               |
| 阿里云镜像下载 (例子:下载**[alpine-standard-3.17.0-x86_64.iso](https://mirrors.aliyun.com/alpine/v3.17/releases/x86_64/alpine-standard-3.17.0-x86_64.iso)**) | [link](https://developer.aliyun.com/mirror/alpine?spm=a2c6h.13651102.0.0.3e221b11zoYtu7) [link](https://mirrors.aliyun.com/alpine/?spm=a2c6h.13651104.0.0.6be55b1fskPrPR) [link](https://mirrors.aliyun.com/alpine/latest-stable/releases/x86_64/?spm=a2c6h.25603864.0.0.1339595dVfgynL) |



# vmware虚拟机安装**Alpine**

**资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 博客参考 | [link](https://blog.csdn.net/lxyoucan/article/details/117153780)  [link](https://blog.csdn.net/m0_70403365/article/details/124758831) [link](https://zhuanlan.zhihu.com/p/107963371) |



**<font color="red">持久化到磁盘</font>**

> 默认alpine系统只运行在内存中，重启后一切数据都将消失,所以我们需要将系统写入磁盘

```shell
setup-alpine         # 此命令用于设置系统
```

> 然后选择硬盘，我这里就一块硬盘，所以直接输入名称 `sda`，当做系统盘，后面输入 `sys`，按照提示确定格式化即可：

![image-20221220155007659](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20221220155007659.png)