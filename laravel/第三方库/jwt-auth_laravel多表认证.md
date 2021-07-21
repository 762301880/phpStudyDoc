# 说明&资料

## 说明





## 资料

| name              | url                                                          |
| ----------------- | ------------------------------------------------------------ |
| jwt-auth Wiki文档 | [link](https://jwt-auth.readthedocs.io/en/develop/laravel-installation/) |
| packagist项目地址 | [link](https://packagist.org/packages/tymon/jwt-auth)        |



# 使用实例

## 安装

- 使用composer安装

```php
composer require tymon/jwt-auth
```

- 添加服务提供者

>1. 添加服务提供者（Laravel 5.4 或以下）
>
>2. 将服务提供者添加到配置文件中的`providers`数组中`config/app.php`，如下所示：

```php
# 在项目目录\config\app.php中的providers中添加
'providers' => [
    ...
    Tymon\JWTAuth\Providers\LaravelServiceProvider::class,
]
```

- 发布配置

> 1. 运行以下命令以发布包配置文件：
> 2. 您现在应该有一个`config/jwt.php`文件，允许您配置此包的基础知识。

```php
php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\LaravelServiceProvider"
```



- 生成密钥

> 1. 我已经包含了一个帮助命令来为你生成一个密钥：
> 2. 这将`.env`使用类似的内容更新您的文件`JWT_SECRET=foobar`
> 3. 这是将用于签署您的令牌的密钥。这究竟如何发生将取决于您选择使用的算法。

```php
php artisan jwt:secret
# 上诉命令会在.env中生成
   
JWT_SECRET=mNkE9Rba3lH0LxvaHFu6Mx0H6I37JXP4nLW1KI3vVCjuaIwBOyib3QLgjGCFrufz
```

