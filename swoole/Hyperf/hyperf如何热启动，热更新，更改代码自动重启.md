#  说明

- 参考[博客](https://www.cnblogs.com/heijinli/articles/14944264.html)

> 使用hyperf开发的时候因为启动了服务代码不能及时更新，难道我们要一直重启服务吗

# 安装

> 注意以下所有操作都是在容器内部操作，

## 使用composer安装

```shell
composer require hyperf/watcher --dev
```

- 生成配置文件
- 所在目录：config/autoload/watcher.php

```php
php bin/hyperf.php vendor:publish hyperf/watcher
```

![img](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/1599860-20210628140812483-1682197992.png)

## 启动

```shell
php bin/hyperf.php server:watch
```



