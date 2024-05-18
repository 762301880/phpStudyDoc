# **说明&&资料**

**说明**

> 无意中从网上看到有那种可以将微信收款码还有支付宝收款码合二为一的功能，原理我看懂了一点点但是
>
> 第一步就是解析二维码,虽然现在网上第三方的解析[草料二维码解析](https://cli.im/deqr/)都很好用，但是我想用代码去实现，查了一下
>
> 有这么一种类库**phpqrcode**
>
> **网上看的案例**
>
> https://cloud.tencent.com/developer/article/1550131?from=article.detail.1193215
>
> https://cloud.tencent.com/developer/article/1193215

**资料**

| 名称                        | 地址                                                         |
| --------------------------- | ------------------------------------------------------------ |
| phpqrcode官网      类库下载 | [link ](http://phpqrcode.sourceforge.net/)  [download](https://sourceforge.net/projects/phpqrcode/files/) |

# **使用示例**

> 本文全部以**laravel**为案例，去使用phpqrcode
>
> **注意**
>
> PHP环境必须开启支持GD2扩展库支持

## 引入类库

**将下载的类库直接赋值到喜欢的目录中**

> 例如我将类库放在**app\Lib**目录中

![1650003912(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/5zw2FdpNVhk6tX8.png)

**引用类库**

> 以下两个引入哪一个都行

```php
require dirname(__DIR__) . '/../Lib' . '/phpqrcode/qrlib.php';
#require dirname(__DIR__).'/../Lib'.'/phpqrcode/phpqrcode.php';
```

