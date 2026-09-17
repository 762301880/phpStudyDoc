## 镜像地址

| 名称                                                         | 地址                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| alpine官网-官网下载贼慢不推荐                                | [link](https://www.alpinelinux.org/downloads/)               |
| 阿里云镜像下载 (例子:下载**[alpine-standard-3.17.0-x86_64.iso](https://mirrors.aliyun.com/alpine/v3.17/releases/x86_64/alpine-standard-3.17.0-x86_64.iso)**) | [link](https://developer.aliyun.com/mirror/alpine?spm=a2c6h.13651102.0.0.3e221b11zoYtu7) [下载地址](https://mirrors.aliyun.com/alpine/?spm=a2c6h.13651104.d-4015.1.6be53721RMqdep) [link](https://mirrors.aliyun.com/alpine/latest-stable/releases/x86_64/?spm=a2c6h.25603864.0.0.1339595dVfgynL) |
| 中科大镜像站-alpine                                          | [link](https://mirrors.ustc.edu.cn/alpine/)  [win](https://mirrors.ustc.edu.cn/alpine/v3.24/releases/x86_64/) |

## vmware虚拟机安装**Alpine**

**资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 博客参考 | [link](https://blog.csdn.net/lxyoucan/article/details/117153780)  [link](https://blog.csdn.net/m0_70403365/article/details/124758831) [link](https://zhuanlan.zhihu.com/p/107963371) |

### **<font color="red">持久化到磁盘(*超级重要)</font>**

> 默认alpine系统只运行在内存中，重启后一切数据都将消失,所以我们需要将系统写入磁盘

```shell
setup-alpine         # 此命令用于设置系统
```

> 然后选择硬盘，我这里就一块硬盘，所以直接输入名称 `sda`，当做系统盘，后面输入 `sys`，按照提示确定格式化即可：

![image-20221220155007659](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20221220155007659.png)

---

## VirtualBox虚拟机安装Alpine

### 注意事项

**操作系统填写**

选择**Linux(64位)**

> 操作系统发行版 选择**Oracle Linux**
>
> 操作系统版本 **Oracle Linux(64-bit)**

![image-20260912160241037](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20260912160241037.png)

### 虚拟机开启后鼠标消失

按一下**Ctrl**案件即可回归 注意是**home键盘旁边的Ctrl案件 不是 键盘左下角的Ctrl**

### 虚拟机屏幕大小设置

点击导航栏的**视图→虚拟显示屏1→缩放至175%(输出自动缩放)**

### 持久化到磁盘之后重启还是没有持久化问题(重)

#### 选择磁盘【重点】

```
Which disk(s) would you like to use? (or '?' for help or 'none') [none]
```

- VMware / VirtualBox：输入 `/dev/sda`
- KVM/Proxmox：输入 `/dev/vda` 输入磁盘名，回车。

#### 安装模式【重点】

```
How would you like to use it? (sys / data / lvm) [?]
```

输入：`sys` 回车，这就是完整持久化安装。

#### 确认擦除磁盘

```
WARNING: Erase the above disk /dev/xxx and continue? [y/N]
```

输入：`y` 回车，确认清空虚拟磁盘。

#### 等待自动安装

脚本自动分区、格式化、安装系统包，直到输出： `Installation is complete`

#### 最后执行重启

```
reboot
```

> ⚠️ 虚拟机设置：**移除 ISO 镜像**，硬盘放到第一启动项，不然重启又进 live 安装界面。

#### 移除ISO镜像

操作顺序：

1. 先点【存储】，清空光驱 ISO
2. 再点【系统】→【主板】，调整启动顺序，硬盘排第一
3. 点【确定】保存设置

> ⚠️ 注意：**必须在执行`reboot`重启虚拟机之前完成上面操作！**

### 开启复制粘贴(主机虚拟机双向复制)

1. 关闭虚拟机，打开虚拟机【设置】→【常规】→【高级】
2. 共享粘贴板：**双向**；拖放：**双向** → 确定保存

### 安装增强功能

**设置→存储→添加安装虚拟机目录下的`VBoxGuestAdditions.iso`**

![image-20260917125710300](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20260917125710300.png)

**手动安装增强包(必须)**

> 挂载目录必须安装这个包

```bash
apk update
apk add virtualbox-guest-additions
```

启用服务

```bash
rc-update add virtualbox-guest-additions boot
rc-service virtualbox-guest-additions start

# 重启虚拟机
reboot

# 重启后验证
VBoxControl sharedfolder list
```

### 挂载本地目录

> 你现在已经：VBoxControl 正常、共享名是 `WWW`，手动 `mount -t vboxsf WWW /www` 可以成功挂载。

#### 直接创建 local.d 启动脚本（就一行挂载命令）

```bash
echo 'mount -t vboxsf WWW /www' > /etc/local.d/mount_www.start
chmod +x /etc/local.d/mount_www.start
rc-update add local default
```

原理：`local.d` 是系统**最后阶段**执行，此时 VBoxService 已经完全就绪，不会时序抢跑。

#### 测试

```bash
rc-service local start
ls /www
```

能看到文件。

重启虚拟机

```bash
reboot
```

开机直接 `ls /www`

> 只有一行脚本，没有依赖、没有循环等待，最简单原生 vboxsf 方案，Alpine 官方文档就是这么写的

#### 如果重启偶尔失效（加个 sleep 1 秒，依然极简）

把脚本改成延迟 1 秒再挂载，只多一个 sleep：

```bash
echo -e '#!/bin/sh\nsleep 1\nmount -t vboxsf WWW /www' > /etc/local.d/mount_www.start
chmod +x /etc/local.d/mount_www.start
rc-update add local default
```

`sleep 1` 等待 VirtualBox 主机通信完全建立，解决偶发时序问题。





---



## Alpine 查询版本命令

```bash
cat /etc/alpine-release
```

---