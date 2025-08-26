#  如果用apache当作服务器无法访问项目

> 请修改**apache** 配置
>
> 项目\public\.htaccess

```shell
<IfModule mod_rewrite.c>
Options +FollowSymlinks -Multiviews
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^(.*)$ index.php?/$1 [QSA,PT,L]
# 添加可以通过request请求获取helder头
SetEnvIf Authorization .+ HTTP_AUTHORIZATION=$0
</IfModule>
```

