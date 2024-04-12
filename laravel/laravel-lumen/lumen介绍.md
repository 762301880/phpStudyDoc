# 一:lumen介绍

>laravel学院[Lumen简介](https://learnku.com/laravel/t/701/laravel-new-members-lumen-powerful-micro-framework)，简单来说就是Lumen速度快，轻量级的laravel框架可以用来编写微小的服务

##  1.1参考资料

| urlName       | address                                    |
| ------------- | ------------------------------------------ |
| Lumen-china   | [链接](https://lumen.laravel-china.org/)   |
| Lumen中文文档 | [链接](https://learnku.com/docs/lumen/6.x) |

# 二: 安装&运行

## 2.1安装

* 我们采用`composer`安装

```php
composer create-project --prefer-dist laravel/lumen blog  #blog==项目名称
```

* 更多安装方法请参考[Lumen文档](https://learnku.com/docs/lumen/6.x/installation/6101#e655a42)

## 2.2运行

* 推荐采用集成环境这里我们采用[phpstudy](https://www.xp.cn/download.html)

![image-20210430095749524](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20210430095749524.png)

* 网站中输入指定的虚拟域名即可访问

> 网页出现以下结果则代表安装配置成功

```html
Lumen (8.2.3) (Laravel Components ^8.0)
```

# 三：简单使用hello world

## 3.1在项目`routes\web.php`中编写代码

```php
# 闭包返回字符
$router->get('hello_world', function () {
    return 'Hello World';
});
```

在浏览器地址栏中输入您配置的虚拟域名+hello_world访问

* 例如http://www.lumen.com/hello_world  出现以下结果则成功

```php+HTML
Hello World
```

## 3.2`注意如果显示以下错误代码`

```shell
Not Found
The requested URL /hello_world was not found on this server.
Additionally, a 404 Not Found error was encountered while trying to use an ErrorDocument to handle the request.
```

### 3.2.1阿帕奇服务器则需要配置.htaccess

* 在项目`public\`.htaccess	中配置
* 参考[配置](https://learnku.com/docs/laravel/8.x/installation/9354#7ad69c)

```shell
<IfModule mod_rewrite.c>
    <IfModule mod_negotiation.c>
        Options -MultiViews -Indexes
    </IfModule>

    RewriteEngine On

    # Handle Authorization Header
    RewriteCond %{HTTP:Authorization} .
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

    # Redirect Trailing Slashes If Not A Folder...
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_URI} (.+)/$
    RewriteRule ^ %1 [L,R=301]

    # Send Requests To Front Controller...
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]
</IfModule>

```

### 3.2.2  nginx 服务器则需要配置优雅链接

* 需要在 nginx中 vhosts.conf中配置
* 参考[配置](https://www.cnblogs.com/yaoliuyang/p/13112042.html)

```
location / {
    try_files $uri $uri/ /index.php?$query_string;
}
```



