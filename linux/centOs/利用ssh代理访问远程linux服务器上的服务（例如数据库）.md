# 说明

> 以前开发的时候是有运维给我们配置开发对应的测试服务器,直接通过**ip地址+端口地址+用户名+密码**访问对应的服务
>
> 即可，新入职了一家公司是通过代理访问的关于代理我目前还不是很理解(它们自己编写了一个服务可以让我们通过打开代理)去访问
>
> 对应的mysql 或者redis服务，感觉用的不是很舒服，网上查了一下有对应的ssh代理软件可以挂后台的那种所以拿过来用
>
> **为什么要这样做**
>
> 因为有的服务器端口不会对外(远程)开放，只能服务器内部本地访问端口,但是我们可以链接服务器权限的时候，
>
> 我们就能通过代理转发的方式在本地访问到其上的服务

# 资料

| 名称                            | 地址                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| **Bitvise SSH Client** 下载地址 | [个人网盘](https://yaoliuyang.lanzoul.com/iJlYY01wa6fa)  [link](https://tunnelier.en.softonic.com/?ex=CAT-3508.5) |
| 参考资料                        | [link](https://blog.csdn.net/qq_16320025/article/details/103962044)   [link](https://www.cnblogs.com/yu29/p/10332891.html)  [link](https://blog.csdn.net/weixin_43860781/article/details/104898684) [link](https://www.likecs.com/show-204714390.html) |





# **配置**

## 启动软件，在Login配置窗口下配置建立ssh连接信息：红框中选择password

![1648004381(1).jpg](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/cXHnrdiUNL6oyWT.png)

## 配置本地端口映射：

> **list.port** 填写自己本地的端口  例如**7306** 是我本地映射的端口 项目中需要用7306连接

![1648004523(1).jpg](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/GtqTnKy3wSd9L6M.png)

# **坑记录**

## 映射端口与本地服务端口重复问题

> 例如上图 如果你想用**6379**访问服务器上面的**6379** redis服务，记得把本地的reids切换端口或者关闭本地端口，再
>
> 或者换一个链接端口