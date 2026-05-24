## 说明

> Nginx部署ThinkPHP项目URL模式不生效的解决方法

> 由于本人使用的是linux开发,在使用nginx搭建了thinkphp的服务发现访问一直是报的根
>
> 目录下面的信息,如何切换路由都没有用,查阅百度得到了解决方案

## 资料

参考[资料](https://www.cnblogs.com/jianzhaojing/articles/11336544.html)

[官方文档](https://doc.thinkphp.cn/v6_1/URLfangwen.html)

## nginx配置

- 本人nginx-thinkphp配置

```php
location / { // …..省略部分代码
   if (!-f $request_filename) {
   		rewrite  ^(.*)$  /index.php?s=/$1  last;
    }
}
```

