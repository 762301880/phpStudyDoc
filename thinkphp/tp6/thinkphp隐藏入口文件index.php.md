# 说明（为什么需要隐藏index.php）

> 在使用**tp**框架的时候最烦人的就是那个入口文件**index.php**
>
> 你使用方法的时候如果不在url中添加**index.php**项目就会无法运行很是烦人关键是不美观

# 解决方案

> 首先找到你的项目中的**public/.htaccess**打开然后找到下面有index.php的这一行 在后面添加一个?号 
>
> 或者直接复制以下代码进去你的项目

```php
<IfModule mod_rewrite.c>
Options +FollowSymlinks -Multiviews
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^(.*)$ index.php?/$1 [QSA,PT,L]
</IfModule>
```

# 之后你就可以正常的使用你的项目了

```php
# 例如你之前访问项目
127.0.0.1/index.php/控制器名称/类名称/方法名称
# 之后就直接可以    
127.0.0.1/控制器名称/类名称/方法名称
```



