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

