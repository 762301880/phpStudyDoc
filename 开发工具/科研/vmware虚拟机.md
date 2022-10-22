# [安装ubuntu](https://blog.csdn.net/Passerby_Wang/article/details/123745479)

> 这种比较属于无脑操作了不做叙述请自行百度查阅安装方法

## 解决虚拟机ubuntu显示过小

> **鼠标右键显示设置->显示器** 慢慢调试分辨率即可

![image-20221008110052749](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20221008110052749.png)

## xshell连接ubuntu虚拟机

**资料**

| 名称 | 地址                                                         |
| ---- | ------------------------------------------------------------ |
| 博客 | [link](https://blog.csdn.net/weixin_45329799/article/details/120276880) |

### **如果xshell出现ssh服务器拒绝了密码,请再试一次**

[![xtedpt.png](https://s1.ax1x.com/2022/10/10/xtedpt.png)](https://imgse.com/i/xtedpt)

**设置步骤**

```shell
# 安装sshd        https://blog.csdn.net/m0_46392035/article/details/124835203

sudo apt install sshd # 如果此命令不行-切换软件源(点击左下角导航栏的-显示应用程序-软件和更新中设置)后执行以下命令
sudo apt install openssh-server
# 启动ssh
systemctl start sshd
#查看sshd是否启用成功
root@yly-ununtu:/# ps -ef | grep sshd
#-----------------------
# root用户被拒绝连接--解决办法如下
sudo vim /etc/ssh/sshd_config    #编辑ssh配置文件，添加如下配置
PermitRootLogin yes
PasswordAuthentication yes

# shell地址就是ip地址不知道的可以ifconfig命令查看
```

#  本地访问虚拟机ubuntu内部项目

**参考资料**

| 名称 | 地址                                                         |
| ---- | ------------------------------------------------------------ |
| 博客 | [link](https://blog.csdn.net/qq_43688965/article/details/121529839) |

## 配置

**虚拟机中查询ip地址**

![image-20221022110847290](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20221022110847290.png)

**虚拟机网络设置修改为NET模式**

![image-20221022110452953](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20221022110452953.png)

> 点击导航栏的**编辑-虚拟网络编辑器**  更改设置

![image-20221022110615424](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20221022110615424.png)

**添加对外访问端口**

![image-20221022110757281](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20221022110757281.png)

> 设置完毕即可访问容器内部项目**如果nginx代理**记得修改配置文件ip为网卡地址例如**192.168.112.128.conf**  即可本地访问容器内部项目

# bug解析

## [解决VMware 与Device/Credential Guard不兼容](https://jingyan.baidu.com/article/cdddd41c7c545312cb00e183.html)

**关闭hyper-V**

> **找到控制面板-> 启用活关闭windows功能**  找到Hyper-v 点击取消确定

![image-20221008090500767](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20221008090500767.png)

**以管理员身份运行打开终端**

> **注意:记得运行命令关闭虚拟机后记得重启电脑**

![image-20221008090727039](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20221008090727039.png)

运行命令关闭hyper-v虚拟机

```shell
bcdedit /set hypervisorlaunchtype off
```

