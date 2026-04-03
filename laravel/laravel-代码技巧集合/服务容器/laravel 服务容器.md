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







#  方法解析

## `$app->make()` 是干嘛的

`$app->make()` 是 **Laravel 框架里的「容器解析 / 实例创建方法」**，核心作用只有一个：

**从 Laravel 的服务容器里，「造出 / 取出」一个类的实例对象。**

可以把它理解成：**Laravel 自带的「万能工厂」**。

### 超通俗比喻

Laravel 有个大箱子叫 **服务容器**，里面存着所有类的「制造图纸」。

- 你想要一个 `UserController`、`DB`、`Log` 或者自定义类的对象
- 不用自己 `new Class()`，直接喊 `$app->make(类名)`
- 容器就会自动帮你：**创建对象 → 处理依赖 → 给你能用的实例**

### 最直观的代码对比

### 1. 普通 PHP 写法（自己 new）

```php
// 自己手动创建实例
$userService = new UserService();
```

2. Laravel 容器写法（$app->make）

```php
// 让 Laravel 容器帮你创建实例
$userService = $app->make(UserService::class);
```

**效果完全一样**，但 `make()` 更强大。

**自动处理依赖**

如果类需要其他类才能运行，`make()` 会自动把依赖创建好传给它。

```php
// UserService 依赖 UserRepository
class UserService {
    public function __construct(UserRepository $repo) {}
}

// 直接 new 必须手动传依赖
$repo = new UserRepository();
$service = new UserService($repo);

// make() 自动搞定，一行搞定
$service = $app->make(UserService::class);
```

**单例复用**

如果你绑定了单例，`make()` 每次都返回**同一个对象**，不会重复创建。

**接口自动映射实现类**

容器里绑定了接口和实现，`make(接口名)` 就能直接拿到实现对象。

### 常见使用场景

1. 在**服务提供者**里解析类
2. 在**命令行脚本**里获取 Laravel 组件
3. 手动创建不适合用依赖注入的类

示例：

```php
// 获取日志实例
$log = $app->make('log');
$log->info('Hello');

// 获取数据库实例
$db = $app->make('db');

// 获取自定义类实例
$myClass = $app->make(MyClass::class);
```

### 简化写法（日常更常用）

Laravel 提供了全局助手函数，和 `$app->make()` 完全等价：

```php
// 这两行效果一模一样
$obj = $app->make(Class::class);
$obj = app(Class::class); // 👈 推荐用这个，更简洁
```

------

### 总结

1. **`$app->make()` = 从 Laravel 容器创建 / 获取类实例**
2. 替代手动 `new`，自动处理依赖、单例、接口绑定
3. 日常开发直接用 `app(类名)` 更方便

# 补充

### 小白必记 4 条规则

1. **绑定都写在服务提供者的 `register()` 里**Laravel
2. **单例用 `singleton()`，每次新实例用 `bind()`**
3. **构造函数类型提示 = 自动注入**，多数场景不用手动 bind
4. **接口绑定**：`bind(接口, 实现)`，代码解耦、好切换、好测试Laravel