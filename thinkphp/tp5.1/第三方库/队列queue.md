# 资料

| 名称           | 地址                                                         |
| -------------- | ------------------------------------------------------------ |
| 第三方博客参考 | [link](https://blog.csdn.net/will5451/article/details/80434174) |
| github官方文档 | [link](https://github.com/top-think/think-queue/tree/2.0)    |

#  安装&使用

**安装**

> 使用composer 命令安装

```php
composer require topthink/think-queue=2.*
```

**配置文件**

>配置文件位于 `config/queue.php`

### 公共配置

```php
[
    'connector'=>'sync' //驱动类型，可选择 sync(默认):同步执行，database:数据库驱动,redis:Redis驱动,topthink:Topthink驱动
                   //或其他自定义的完整的类名
]
```

### 驱动配置

> 各个驱动的具体可用配置项在`think\queue\connector`目录下各个驱动类里的`options`属性中，写在上面的`queue`配置里即可覆盖

