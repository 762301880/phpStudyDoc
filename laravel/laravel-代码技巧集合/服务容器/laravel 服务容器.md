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

## app()->make 与 $this->app->singleton区别

```php
// 第一次 make
$a = app()->make(Service::class);

// 第二次 make
$b = app()->make(Service::class);

// 他俩是同一个实例吗？
var_dump($a === $b); // false（默认每次新建）
```

如果你在服务提供者里绑定单例：

```php
$this->app->singleton(Service::class);

var_dump($a === $b); // true（同一个对象）
```

# Laravel 容器：bind /singleton/instance 超通俗讲解

这三个都是 **Laravel 服务容器** 的核心方法，作用只有一个：**教容器怎么 “造”/“拿” 对象**，区别只是**创建时机**和**是否复用同一个对象**。

我用最直白的话 + 带注释代码给你讲懂，保证一看就会。

------

## 核心口诀

1. **`bind`**：每次拿，都**新建一个对象**（每次都新的）
2. **`singleton`**：只造**一次**，后面永远拿**同一个对象**（单例）
3. **`instance`**：我已经**手动造好对象**了，容器你直接用这个（已存在的实例）

------

## 1. `$this->app->bind()` 绑定（每次都新建）

### 作用

告诉容器：**当有人要拿这个类时，每次都执行闭包，创建一个新对象给它。**

### 适用场景

需要**每次都全新实例**的类（比如工具类、请求级别的类）。

```php
// 绑定：告诉容器，想要 Tool 类，就 new 一个新的
$this->app->bind('Tool', function ($app) {
    return new Tool(); // 每次调用都会执行这里，新建对象
});

// 第一次拿：新建对象
$tool1 = app('Tool');
// 第二次拿：又新建一个对象
$tool2 = app('Tool');

// 结果：两个不是同一个对象（地址不一样）
var_dump($tool1 === $tool2); // false
```

------

## 2. `$this->app->singleton()` 单例绑定（只造一次）

### 作用

告诉容器：**第一次拿的时候创建对象，后面所有人都共用这同一个对象。**

### 适用场景

全局只需要一个的类（日志、配置、数据库连接）。

```php
// 单例绑定：只新建一次，后面永远复用
$this->app->singleton('Logger', function ($app) {
    return new Logger(); // 只在第一次调用时执行这里
});

// 第一次拿：新建对象
$logger1 = app('Logger');
// 第二次拿：直接返回第一次创建的那个，不新建
$logger2 = app('Logger');

// 结果：完全是同一个对象
var_dump($logger1 === $logger2); // true
```

------

## 3. `$this->app->instance()` 实例绑定（用我现成的）

### 作用

**我已经手动 new 好对象了**，容器你别自己造了，就用我给你的这个实例。

和 `singleton` 一样也是单例，但**对象是你提前创建好的**。

### 适用场景

- 已经实例化好的对象
- 测试时传入模拟对象
- 提前配置好参数的对象

```php
// 1. 我自己先手动 new 好对象
$myLogger = new Logger();
$myLogger->setPath('/log/test.log'); // 提前配置好

// 2. 把这个现成的实例丢给容器
$this->app->instance('Logger', $myLogger);

// 3. 拿出来的永远是我刚才创建的那个
$logger1 = app('Logger');
$logger2 = app('Logger');

var_dump($logger1 === $logger2); // true
var_dump($logger1->getPath()); // /log/test.log
```

------

## 一张表彻底分清

|     方法      | 何时创建对象 | 是否复用同一个 |            场景            |
| :-----------: | :----------: | :------------: | :------------------------: |
|   `bind()`    |  每次获取时  |  ❌ 每次新对象  |        无状态工具类        |
| `singleton()` | 第一次获取时 |  ✅ 全局同一个  |         全局单例类         |
| `instance()`  | 绑定前已创建 | ✅ 用你给的实例 | 已存在对象、测试、提前配置 |

------

## 最简单的记忆法

- 要**每次新的** → `bind`
- 要**全局共用** → `singleton`
- **对象已经有了** → `instance`

------

### 总结

1. **`bind`**：每次获取都**新建对象**
2. **`singleton`**：只创建**一次**，全局**共用同一个**
3. **`instance`**：使用**你手动创建好**的现成实例

# 补充

### 小白必记 4 条规则

1. **绑定都写在服务提供者的 `register()` 里**Laravel
2. **单例用 `singleton()`，每次新实例用 `bind()`**
3. **构造函数类型提示 = 自动注入**，多数场景不用手动 bind
4. **接口绑定**：`bind(接口, 实现)`，代码解耦、好切换、好测试Laravel

#  instance  超详细解释

instance 到底是干嘛？

**bind 和 singleton 是：告诉容器 “怎么造” 对象**

**instance 是：直接把 “造好的对象” 丢给容器**

生活例子（一看就懂）

场景：你要喝水

- **bind**：给你一个**水壶**，你要喝水，我就**现烧一壶**给你
- **singleton**：只烧**一壶水**，你们所有人都喝这一壶
- **instance**：**我已经烧好一壶水了**，直接放你桌上，你就喝这一壶！

代码对比（超级简单）

1）bind（每次都新造）

```php
// 告诉容器：要可乐，就 new 一瓶
$this->app->bind('可乐', function () {
    return new 可乐();
});

$a = app('可乐'); // 新的一瓶
$b = app('可乐'); // 又新的一瓶
// a 和 b 不是同一瓶
```

2）singleton（只造一次）

```php
// 告诉容器：只造一瓶可乐，大家共用
$this->app->singleton('可乐', function () {
    return new 可乐();
});

$a = app('可乐'); // 第一次：造一瓶
$b = app('可乐'); // 第二次：还用刚才那瓶
// a 和 b 是同一瓶
```

3）instance（我已经造好了！）

```php
// 我自己先手动 new 好一瓶！
$myCola = new 可乐();
$myCola->加冰();    // 我自己配置好
$myCola->加糖();

// 直接把这瓶“现成的”丢给容器
$this->app->instance('可乐', $myCola);

// 以后谁拿，都是我这瓶加冰加糖的！
$a = app('可乐');
$b = app('可乐');
// a 和 b 都是我刚才那一瓶！
```

最关键区别

- **bind / singleton**：容器**自己会 new 对象**
- **instance**：**容器不 new 对象**，对象是**你提前 new 好的**

再简化到 3 句人话

1. **bind** = 每次都**新做**
2. **singleton** = 只**做一次**，一直用
3. **instance** = **我做好了**，你直接用这个

你什么时候用 instance？

只要满足下面任意一条，就用 instance：

1. 对象**你已经提前创建好了**
2. 对象需要**提前配置**（加冰、加糖、设参数）
3. 测试时要**传入假对象**

总结（最简单版）

- **bind**：每次新对象
- **singleton**：共用一个对象
- **instance**：**用我现成的对象**

