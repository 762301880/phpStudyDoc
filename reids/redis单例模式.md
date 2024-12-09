## [菜鸟教程-设计模式之单例模式](https://www.runoob.com/design-pattern/singleton-pattern.html)

### 单例模式（Singleton Pattern）

### 意图

确保一个类只有一个实例，并提供一个全局访问点来访问该实例。

### 主要解决

频繁创建和销毁全局使用的类实例的问题。

### 何时使用

当需要控制实例数目，节省系统资源时。

### 如何解决

检查系统是否已经存在该单例，如果存在则返回该实例；如果不存在则创建一个新实例。

### 关键代码

构造函数是私有的。

### 应用实例

- 一个班级只有一个班主任。
- Windows 在多进程多线程环境下操作文件时，避免多个进程或线程同时操作一个文件，需要通过唯一实例进行处理。
- 设备管理器设计为单例模式，例如电脑有两台打印机，避免同时打印同一个文件。

### 优点

- 内存中只有一个实例，减少内存开销，尤其是频繁创建和销毁实例时（如管理学院首页页面缓存）。
- 避免资源的多重占用（如写文件操作）。

### 缺点

- 没有接口，不能继承。
- 与单一职责原则冲突，一个类应该只关心内部逻辑，而不关心实例化方式。

### 使用场景

- 生成唯一序列号。
- WEB 中的计数器，避免每次刷新都在数据库中增加计数，先缓存起来。
- 创建消耗资源过多的对象，如 I/O 与数据库连接等。

### 注意事项

- **线程安全**：`getInstance()` 方法中需要使用同步锁 `synchronized (Singleton.class)` 防止多线程同时进入造成实例被多次创建。
- **延迟初始化**：实例在第一次调用 `getInstance()` 方法时创建。
- **序列化和反序列化**：重写 `readResolve` 方法以确保反序列化时不会创建新的实例。
- **反射攻击**：在构造函数中添加防护代码，防止通过反射创建新实例。
- **类加载器问题**：注意复杂类加载环境可能导致的多个实例问题。

### 结构

单例模式包含以下几个主要角色：

- 单例类：包含单例实例的类，通常将构造函数声明为私有。
- 静态成员变量：用于存储单例实例的静态成员变量。
- 获取实例方法：静态方法，用于获取单例实例。
- 私有构造函数：防止外部直接实例化单例类。
- 线程安全处理：确保在多线程环境下单例实例的创建是安全的。

###   特点

- 唯一性：一个类只有一个实例。
- 全局访问：提供一个全局访问点，方便获取实例。
- 延迟初始化：实例在第一次被请求时才创建，而不是在程序启动时创建。

### PHP 中实现单例模式

```php
class Singleton {
    // 私有静态变量存储唯一实例
    private static $instance = null;

    // 私有构造函数，防止外部实例化
    private function __construct() {}

    // 私有克隆方法，防止克隆实例
    private function __clone() {}

    // 私有唤醒方法，防止反序列化创建新实例
    private function __wakeup() {}

    // 静态方法返回唯一实例
    public static function getInstance() {
        if (self::$instance === null) {
            self::$instance = new self();
        }
        return self::$instance;
    }

    // 示例方法
    public function doSomething() {
        echo "Doing something...\n";
    }
}
```

**使用**

```php
// 获取单例实例
$singleton1 = Singleton::getInstance();
$singleton2 = Singleton::getInstance();

// 检查是否为同一个实例
var_dump($singleton1 === $singleton2); // 输出: bool(true)

// 调用方法
$singleton1->doSomething(); // 输出: Doing something...
```



## 什么是单例模式

> 单例模式是一种创建型模式，在一个系统中只有一个实例对象，且该实例可以被系统中的任何代码所访问和修改。单例模式的目的在于保证系统中一个类只有一个实例，并提供一个访问它的全局访问点。
>
> 单例模式适用于一些被重复使用的对象，例如全局日志对象、全局配置对象、数据库连接等。从实现角度来说，许多现代编程语言都提供了线程安全、饿汉式、懒汉式等多种单例模式的实现方式。
>
> 单例模式的优点在于：
>
> - 提供了对唯一实例的受控访问，避免了多个实例竞争资源造成的冲突问题，确保了全局状态的一致性。
> - 由于单例对象只有一个实例，因此可以节省系统资源、降低系统复杂度、提高系统的可维护性。
> - 单例模式可以允许客户端（调用者）自由访问唯一实例，方便系统的扩展和修改。
>
> 在一些情况下，滥用单例模式可能会带来一些问题，比如：
>
> - 单例模式可能被滥用为全局变量，导致系统中的代码紧密耦合、难以维护、难以测试。
> - 单例模式对扩展开放，对修改封闭，对于一些频繁变化的场景（如并发操作、多线程处理等），使用单例模式会带来一些不必要的限制。
> - 单例模式在实现上可能会产生一些竞争和线程同步问题，需要仔细处理。
>
> 因此，在使用单例模式时需要注意平衡其优点与缺点，避免滥用。

## laravel实现单例模式

> 1. 创建一个 Redis 单例类，我们可以将它定义在 `app/Helpers` 目录下，比如 `RedisSingleton.php`

```php
<?php

namespace App\Helpers;

use Illuminate\Support\Facades\Redis;

class RedisSingleton
{
    private static $instance = null;

    private function __construct()
    {
        // 私有化构造函数以避免类被实例化，只能通过 getInstance 方法获得类的唯一实例。
    }

    public static function getInstance()
    {
        if (is_null(self::$instance)) {
            self::$instance = Redis::connection()->client();
        }
        return self::$instance;
    }

    public function __call($method, $arguments)
    {
        return call_user_func_array([self::getInstance(), $method], $arguments);
    }
}
```

> 在这个 Laravel 的 Redis 单例实现中，`__call`方法是用于转发 Redis 实例的调用的。具体来说，当我们通过单例的 `getInstance` 方法获取到 Redis 实例时，如果我们想要使用某个 Redis 方法，可以直接通过该实例调用，比如：

```
$redis = RedisSingleton::getInstance();
$redis->set('foo', 'bar');
echo $redis->get('foo'); // 输出 'bar'
```

> 但是，如果我们想要调用 Redis 连接对象上不存在的方法，这时候如果直接使用 `$redis->nonexistentMethod()` 会触发“调用未定义的方法”错误。因此在 Redis 单例类中我们定义了 `__call` 方法，它将任何需要调用 Redis 方法的请求委托给单例的 Redis 连接处理，保证在应用中调用 Redis 的任何方法都经过单例处理，避免出现异常或错误。

> 在 `config/database.php` 文件中配置 Redis 连接参数

```php
'redis' => [
    // ...
    'default' => [
        'url' => env('REDIS_URL'),
        'host' => env('REDIS_HOST', '127.0.0.1'),
        'password' => env('REDIS_PASSWORD', null),
        'port' => env('REDIS_PORT', '6379'),
        'database' => env('REDIS_DB', '0'),
    ],
    // ...
],
```

> 在 Laravel 项目中使用 Redis 时，只需通过以下方式获取 Redis 实例

```php
$redis = RedisSingleton::getInstance();
$redis->set('foo', 'bar');
echo $redis->get('foo'); // 输出 'bar'
```

