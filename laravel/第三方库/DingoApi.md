# 资料

| name                         | url                                                          |
| ---------------------------- | ------------------------------------------------------------ |
| DingoApi-laravel学院官网地址 | [link](https://learnku.com/docs/dingo-api/2.0.0/Installation/1443) |
|                              |                                                              |



# 安装&使用

## 安装

- 使用composer安装

```shell
composer require dingo/api
```

- 添加服务提供者

> 在`config/app.php`中的providers中添加以下代码

```shell
'providers' => [
    Dingo\Api\Provider\LaravelServiceProvider::class,
 ]
```

- 发布配置文件

```shell
php artisan vendor:publish --provider="Dingo\Api\Provider\LaravelServiceProvider"
```

- Facades

  > API 自带了两个 Facade，你可以酌情使用。

```shell
Dingo\Api\Facade\API# 这个是调度器的 Facade ，并提供了一些好用的辅助方法。
Dingo\Api\Facade\Route # 你可以使用这个 Facade 来获取 API 的当前路由、请求、检查当前路由的名称等。
```

- 配置

>在 .env（推荐）或则在 `config\api.php`中配置
>
>  请查阅[官方文档](https://learnku.com/docs/dingo-api/2.0.0/Configuration/1444)

     ```shell
     # 配置api前缀如果要使用dingoApi的前缀一定要配置
     API_PREFIX=api 
     # 版本号这个版本号是你的 API 的默认版本号，并且会在一些未提供版本号的情况下作为回调的默认值使用。在生成 API 文档时也会使用这个版本号作为默认值。
     API_VERSION=v1 
     # 配置api名称
     API_NAME="My API"     
     API_CONDITIONAL_REQUEST=false
     # 严格模式 严格模式要求客户端发送 Accept 头，代替配置文件中配置的默认版本。这意味着你将不能通过浏览器直接访问你的 API。
     API_STRICT=false
     # api响应格式这里设置为json
     API_DEFAULT_FORMAT=json  
     # 调试模式请本地开启线上设置为:false
     API_DEBUG=true 
     ```



## 使用

 ### 创建端点

一个端点是一个路由的另一种说法。当讨论 API 的时候，很多人把访问的路由称作为一个端点。

> 为了避免与你主要的项目路由冲突，dingo/api 将会使用其专属的路由实例。要创建端点，我们首先需要获得一个 API 路由的实例
>
> 请在`routes\api.php`中创建端点

```php
$api = app('Dingo\Api\Routing\Router');
```

- 定义一个版本分组

> 这种定义方式有利于后续为相同端点新增多版本支持

```shell
# \Dingo\Api\Routing\Router t
$api->version('v1', function (\Dingo\Api\Routing\Router $api) {

});
```

- 更多[使用方式](https://learnku.com/docs/dingo-api/2.0.0/Creating-API-Endpoints/1445)

```shell
# 如果你想一个分组返回多个版本，只需要传递一个版本数组。
$api->version(['v1', 'v2'], function ($api) {

});
# 通过在第二个参数上传递一个属性数组，你也可以将此组视为特定框架的标准组。
$api->version('v1', ['middleware' => 'foo'], function ($api) {

});
# 你还可以嵌套常规组以进一步定制某些端点。
$api->version('v1', function ($api) {
    $api->group(['middleware' => 'foo'], function ($api) {
        // Endpoints registered here will have the "foo" middleware applied.
    });
});
# 创建端点#
# 一旦你有了一个版本分组，你就可以在分组闭包的参数中，通过 $api 创建端点。
$api->version('v1', function ($api) {
    $api->get('users/{id}', 'App\Api\Controllers\UserController@show');
});
# 因为端点被每个版本分组了，你可以为相同 URL 上的同一个端点创建不同响应。
$api->version('v1', function ($api) {
    $api->get('users/{id}', 'App\Api\V1\Controllers\UserController@show');
});

$api->version('v2', function ($api) {
    $api->get('users/{id}', 'App\Api\V2\Controllers\UserController@show');
});
# 你也可以使用各自的方法注册资源和控制器。

# 提醒，你需要为控制器添加说明完整的命名空间，比如， App\Http\Controllers.

# 命名路由和生成 URLs#
# 命名你的路由可以使你方便的生成他们的 URL。你可以跟 Laravel 一样的方法命名你的路由。

$api->get('users/{id}', ['as' => 'users.index', 'uses' => 'Api\V1\UserController@show']);

# 现在你可以为命名的路由生成 URL 了。

app('Dingo\Api\Routing\UrlGenerator')->version('v1')->route('users.index');Copy

#你需要提供一个版本，这样才能基于这个版本的路由生成正确的 URL。并且允许你在不同版本中使用相同的名字。

# 在命令行中查看路由#
# 如果你正在使用 Laravel 5.1 你可以使用 Artisan 命令看到你注册的路由。

$ php artisan api:routes

# 这个命令和 Laravel 自带的 route:list 命令相同。
```

