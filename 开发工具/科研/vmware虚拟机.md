# [安装ubuntu](https://blog.csdn.net/Passerby_Wang/article/details/123745479)

> 这种比较属于无脑操作了不做叙述请自行百度查阅安装方法









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

