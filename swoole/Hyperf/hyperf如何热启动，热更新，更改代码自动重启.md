#  说明

- 参考[博客](https://www.cnblogs.com/heijinli/articles/14944264.html)

> 使用hyperf开发的时候因为启动了服务代码不能及时更新，难道我们要一直重启服务吗

## 资料

| 名称                 | 地址                                                |
| -------------------- | --------------------------------------------------- |
| 热更新 Watcher- 官网 | [link](https://www.hyperf.wiki/2.1/#/zh-cn/watcher) |



# 安装

> 注意以下所有操作都是在容器内部操作，

## 使用composer安装

```shell
composer require hyperf/watcher --dev
# 如果提示版本过大与当前hyperf不兼容可以安装低一点的版本
composer require hyperf/watcher=2.1.* --dev 
```

- 生成配置文件
- 所在目录：config/autoload/watcher.php

```php
php bin/hyperf.php vendor:publish hyperf/watcher
```

![img](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/1599860-20210628140812483-1682197992.png)

## 启动

```shell
php bin/hyperf.php server:watch
```



## 补充

> 上面启动完成之后就可以不用每次更新代码的时候再去重启hyperf框架了他会自动更新框架代码
