# 资料

| name                    | url                                                          |
| ----------------------- | ------------------------------------------------------------ |
| laravel服务容器实例教程 | [link](https://laravelacademy.org/post/769.html)             |
| laravel手册- 服务容器   | [link](https://learnku.com/docs/laravel/8.x/container/9361)  |
| 第三方博客              | [link](https://www.qianjinyike.com/laravel-%e6%a0%b8%e5%bf%83%e6%a6%82%e5%bf%b5%ef%bc%88%e6%9c%8d%e5%8a%a1%e5%ae%b9%e5%99%a8%e3%80%81%e6%9c%8d%e5%8a%a1%e6%8f%90%e4%be%9b%e8%80%85%e3%80%81facades%e3%80%81contracts%e3%80%81%e8%be%85/) |

# 核心概念

例子可以参考

```shell
项目根目录/vendor/laravel/framework/src/Illuminate/Cache/CacheServiceProvider.php
```

基础绑定

```shell
# HelpSpot\API 可以是任意名称语义化，此绑定每次使用都会实例化
$this->app->bind('HelpSpot\API', function ($app) {
    return new HelpSpot\API($app->make('HttpClient'));
});
```

绑定单例

```shell
# 第一次绑定之后下次调用的时候不会再次绑定实例化会采用上一
$this->app->singleton('HelpSpot\API', function ($app) {
    return new HelpSpot\API($app->make('HttpClient'));
});
```























