# 说明

> 不得不说tp5.1坑是真多，今天获取 请求头中的**Authorization**值的时候死活获取不到，绝了
>
> 查了百度还好不止我一人碰到这种问题

**参考资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://blog.csdn.net/cpongo3/article/details/95046353) |
|            |                                                              |

> 百度关键词 **thinkphp5.1 如何获取header**

# 解决方案

> 在**项目目录\public\.htaccess**中添加一行配置

```php
<IfModule mod_rewrite.c>
Options +FollowSymlinks -Multiviews
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^(.*)$ index.php?/$1 [QSA,PT,L]
# 增加支持获取header头设置
SetEnvIf Authorization .+ HTTP_AUTHORIZATION=$0  # 增加的一行
</IfModule>
```

