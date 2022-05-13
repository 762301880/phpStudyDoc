# 资料

| 名称                        | 地址                                                         |
| --------------------------- | ------------------------------------------------------------ |
| php-yar官方文档，github说明 | [link](https://www.php.net/manual/zh/book.yar.php) [link](https://github.com/laruence/yar) |
| 第三方博客                  | [link](https://www.laruence.com/2012/09/15/2779.html)  [link](https://blog.csdn.net/gaitiangai/article/details/105821340) [link](https://www.kancloud.cn/a173512/php_note/1690781) |

**说明**

> RPC是远程过程调用（Remote Procedure Call）的缩写形式,**Yar**是一个轻量级的并行的[RPC](https://baike.baidu.com/item/%E8%BF%9C%E7%A8%8B%E8%BF%87%E7%A8%8B%E8%B0%83%E7%94%A8/7854346?fromtitle=RPC&fromid=609861&fr=aladdin)框架，也是一个PHP扩展，

# 安装

**资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://www.cxyzjd.com/article/qq_40848163/109119518) |



## windows 安装

[**下载yar扩展**](https://pecl.php.net/package/yar)

> 请在[**pecl扩展官网下载**](https://pecl.php.net/package/yar),对应**php(不知道版本可以终端php -v查看)**版本

  **点击DLL**下载windows扩展

<img src="https://s2.loli.net/2022/05/13/cBW3qCeNXJitwsS.png" alt="1652404037(1).jpg" style="zoom:50%;" />

**下载对应版本**

>  Thread Safe([线程安全](https://so.csdn.net/so/search?q=线程安全&spm=1001.2101.3001.7020))和 None Thread Safe(NTS，非线程安全)  本人下载的是**None Thread Safe**,**是否线程安全可以打印phpinfo()**
>
> 查询线程是否安全

![1652404126(1).jpg](https://s2.loli.net/2022/05/13/T1nKjFztwLaS8Gy.png)

> 线程安全:禁用

![1652404349(1).jpg](https://s2.loli.net/2022/05/13/dXYoGuwZnV42CSt.png)

下载完成之后解压

> 将**php_yar.dll**文件复制到，**php**项目文件的**ext**扩展包中,由于我使用的是**phpstudy集成环境**所以目录是**D:\phpstudy_pro\Extensions\php\php7.4.3nts\ext**
>
> 然后编辑**php.ini**配置文件中添加**extension=php_yar.dll** 然后重启php&&apache即可

```php
# 查询是否安装成功
C:\Users >php -m
[PHP Modules]
****
yar
****
[Zend Modules]
# or php --ri yar
C:\Users >php --ri yar

yar
    
yar support => enabled
Version => 2.2.1

Directive => Local Value => Master Value
yar.packager => php => php
yar.transport => curl => curl
yar.debug => Off => Off
yar.expose_info => On => On
yar.connect_timeout => 1000 => 1000
yar.timeout => 5000 => 5000
yar.content_type => application/octet-stream => application/octet-stream    
```

# 项目中使用实例

```php

```

## 补充

**判断扩展是否存在**

```php
extension_loaded('yar');  # 判断yar扩展是否存在返回 bool类型
```



# phpstorm对应扩展提示设置

**说明**

> 如果我们安装完成扩展后但是些项目种并没有代码提示(很不幸本人就是离开了提示活不下去的那种),请跟随我设置
>
> 点击**phpstorm**左上角导航栏 **File-Settings** 找到**Languages&Framework -PHP** 先选择对应的语言等级然后再找到下面的
>
> **PHP Runtime 勾选扩展**点击**确定回到编辑器既可以有提示**

![1652405064(1).png](https://s2.loli.net/2022/05/13/3CanPeRZ7sbpvhY.png)