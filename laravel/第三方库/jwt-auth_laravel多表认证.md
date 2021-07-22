# 说明&资料

## 说明[此段说明截取第三方博客](https://laravelacademy.org/post/9794)

### 什么是jwt

> jwt单词代表的意思就是JSON Web Token。
>
> 我们在做api请求的时候，通常要使用token，来验证是否这个请求能不能访问。
>
> 一旦用户登录，后续每个请求都将包含JWT，允许用户访问该令牌允许的路由、服务和资源。单点登录是现在广泛使用的JWT的一个特性，因为它的开销很小，并且可以轻松地跨域使用。

### 为什么使用jwt

> Session是在服务器端的，而JWT是在客户端的。
>
> Session方式存储用户信息的最大问题在于要占用大量服务器内存，增加服务器的开销，而JWT方式将用户状态分散到了客户端中，可以明显减轻服务端的内存压力。



## 资料

| name              | url                                                          |
| ----------------- | ------------------------------------------------------------ |
| 第三方博客        | [link](https://learnku.com/articles/30342)                   |
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

# 待补充

