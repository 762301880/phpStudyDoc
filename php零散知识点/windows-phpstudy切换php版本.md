#  说明

> 本次开发过程中由于有一个扩展包使用的`php`版本比较高所以本地开发的时候
>
> 需要随之改变`php`的使用版本，由于是集成开发环境(使用phpstudy)所以需要改变
>
> 使用版本

# 步骤

> 本人需要从php7.3切换至7.4版本

- 1. 下载php7.4

> 打开phpstudy的***软件管理***-**全部**-**php** 下载***7.4***版本

<img src="https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/AoxGpDvhmVbQ7sX.png" alt="1630455856(1).jpg" style="zoom:50%;" />

- 2. 修改虚拟域名的php版本地址

  > 点击网站-选择自己对应的虚拟域名切换php版本



<img src="https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/FAixcnWkEouwhHI.png" alt="1630455956(1).jpg" style="zoom:50%;" />

- 3 .配置环境变量

  >找到***此电脑***右键点击**属性**-点击**高级系统设置**-**环境变量**-**系统变量**-***Path***-`编辑`,
  >
  >新添加php7.4的目录地址
  >
  >***注意*** 如果之前php7.3或者其他的版本还在环境变量中请**删除**，只要当前需要的版本

  <img src="https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/1VeAIKaNqmUoMtw.png" alt="1630456159(1).jpg" style="zoom: 80%;" />

- 4.重启电脑

  > **(这一步非常关键)**:要不然你虽然再***cmd***重使用**php -v**命令
  >
  > 查看的是最新版本但是使用起来还是php7.3(老版本)

# windows安装php8

## 官网下载[P8版本](https://www.php.net/)  [php8.2.5](https://windows.php.net/downloads/releases/php-8.2.5-nts-Win32-vs16-x64.zip)

##  解压到php目录

![image-20230503092147339](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20230503092147339.png)

## 配置环境变量(配置完成之后重启电脑使变量生效)

> 变量指向解压目录即可,例如**D:\phpstudy_pro\Extensions\php\php-8.2.5-nts-Win32-vs16-x64**

![image-20230503092251357](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20230503092251357.png)

##  PHP配置文件

> 拷贝解压目录下的**php.ini-development** 为**php.ini**

```shell
# 拷贝文件
cp php.ini-development php.ini

# 配置 扩展

extension_dir = "ext"     # 配置扩展目录
extension=openssl         #开启openssl扩展

```

