[created:2022/02/22]

# 说明

> 今天来到了新的公司上班第一天，太久没有使用thinkphp框架，由于是laravel出生 所以很少使用thinkphp
>
> 开发项目 所以今天在调试查看使用项目的时候 熟练的 使用了 dd 函数 可惜的是thinkphp5.1并不支持dd函数
>
> 好在他有一个扩展包可以方便实现 

**[参考资料](https://blog.csdn.net/Do_Only/article/details/99452023)**

# 安装扩展

**composer 安装**

> --dev 开发环境安装 不会提交到代码仓库

```php
composer require symfony/var-dumper --dev  
```

**使用**

> 直接使用打印即可

```php
dd('你的变量即可');
```

