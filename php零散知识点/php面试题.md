## 引用传递和值传递的区别？PHP 中哪些场景会自动触发引用？

> ## 一、先搞懂：值传递 和 引用传递 的本质区别
>
> ### 1. 值传递（传值）
>
> **把变量的「副本」传给函数 / 参数**
>
> - 函数内部操作的是**复制出来的新数据**
> - 函数内外**互不影响**，修改内部不会改变外部原值
> - 默认绝大多数场景都是值传递

代码示例

```php
function add($num) {
    $num += 10; // 修改的是副本
}

$a = 20;
add($a);
echo $a; // 输出 20，原值没变
```

###  引用传递（传址）

**把变量的「内存地址」传给函数 / 参数**

- 函数内外操作的是**同一块内存数据**
- 函数内部修改，**外部原值会同步改变**
- 必须手动加 `&` 符号才是显式引用

代码示例

```php
function add(&$num) { // & 代表引用传递
    $num += 10;
}

$a = 20;
add($a);
echo $a; // 输出 30，原值被修改了
```

### 一句话总结区别

|       对比项       |         值传递         |      引用传递      |
| :----------------: | :--------------------: | :----------------: |
|      传递内容      |     变量的**副本**     | 变量的**内存地址** |
| 内部修改影响外部？ |         不影响         |      直接影响      |
|        性能        | 复制数据，大数据效率低 |   不复制，效率高   |
|    PHP 默认方式    |           是           |   否（必须加 &）   |



### 重点：PHP 中**会自动触发引用**的场景

PHP 有几个**不需要写 &，系统自动使用引用**的特殊场景，这是面试必问：

###  对象赋值 / 对象参数传递（PHP 5+）

**所有对象默认都是引用传递（指针传递）**

哪怕不写 &，函数内修改对象，外部也会变。

```php
class User { public $name = 'Tom'; }
function change($obj) { $obj->name = 'Jerry'; } // 无 &

$user = new User();
change($user);
echo $user->name; // 输出 Jerry，自动引用
```

2. foreach 循环中的 `&$value` 引用遍历

```php
$arr = [1,2,3];
foreach($arr as &$val){ // 这里加了 &
    $val *= 2;
}
// 数组变成 [2,4,6]，原值被直接修改
```

⚠️ 注意：用完必须 `unset($val)` 防止意外覆盖变量。



## PSR-4 自动加载原理

- PSR-4 是 PHP-FIG 制定的自动加载标准，核心是「命名空间前缀 → 目录」映射 + 类名直接转文件名，无需多级冗余目录，Composer 主流实现。

### 一、PSR-4 核心规则

**完整类名格式**

```shell
\<命名空间前缀>(\<子命名空间>)*\<最终类名>
```

**命名空间分隔符**：`\`

**类、接口、Trait 都适用**

**下划线无特殊含义**（区别于 PSR-0）

**映射规则**

1. **命名空间前缀** ↔ **基目录（Base Directory）** 一一对应
2. **子命名空间** ↔ **子目录**（`\` → `/`）PHP-FIG
3. **最终类名** ↔ **文件名.php**（大小写严格一致）PHP-FIG

### 二、文件路径转换原理（示例）

假设 `composer.json` 配置：

```json
{
    "autoload": {
        "psr-4": {
            "App\\": "src/",
            "Foo\\Bar\\": "lib/FooBar/"
        }
    }
}
```

#### 例 1：`App\Http\Controllers\UserController`

1. 匹配前缀 `App\\` → 基目录 `src/`
2. 去掉前缀：`Http\Controllers\UserController`
3. `\` → `/`：`Http/Controllers/UserController`
4. 加 `.php` → **`src/Http/Controllers/UserController.php`**

#### 例 2：`Foo\Bar\Baz\Qux`

1. 前缀 `Foo\Bar\\` → `lib/FooBar/`
2. 剩余：`Baz\Qux`
3. 转换：`Baz/Qux.php`
4. 路径：**`lib/FooBar/Baz/Qux.php`**

### 三、自动加载执行流程（Composer 版）

1. **入口**：`require __DIR__.'/vendor/autoload.php';`

2. 注册 autoloader：

   - Composer 生成 `vendor/composer/autoload_psr4.php`（前缀→目录映射数组）
   - 调用 `spl_autoload_register` 注册加载器PHP-FIG

   

3. **类未找到时触发**：

```php
// 伪代码逻辑
function autoload($class) {
    foreach ($psr4Map as $prefix => $dir) {
        // 检查类名是否以该前缀开头
        if (strpos($class, $prefix) === 0) {
            // 截取相对类名
            $relative = substr($class, strlen($prefix));
            // 拼接路径
            $file = $dir . str_replace('\\', '/', $relative) . '.php';
            // 存在则加载
            if (file_exists($file)) {
                require $file;
                return;
            }
        }
    }
}
​```{insert\_element\_3\_}
```

##   什么是原子操作

简单说：**原子操作 = 不可再拆分、要么全部做完、要么完全不做的操作**，中途不会被打断，也不会出现 “做了一半” 的状态。

### 一、最通俗的理解

你可以把它想象成：

- 普通操作：可以被别人中途插队、打断
- **原子操作**：一旦开始，必须一口气做完，别人插不进来

举个生活例子：

你从银行卡转账 100 元：

1. 扣你账户 100
2. 加对方账户 100

如果不是原子操作：

- 刚扣完你的钱，系统崩了
- 对方没收到钱 → 钱凭空消失

**原子操作**就保证：

要么两步都成功，要么两步都撤销，不会只做一半。

### 二、在编程 / 计算机里的含义

在多线程、并发场景里非常关键：

- 多个线程同时修改同一个变量时
- 普通的 `i++` 其实是三步：读 → 加 → 写
- 中途可能被别的线程打断，导致结果错误

**原子操作**就是把这三步 “打包成一个整体”，保证：

- 同一时刻只有一个线程在执行
- 执行过程不会被中断
- 最终结果一定正确

常见原子操作：

- 原子自增、原子赋值
- CAS（Compare And Swap，比较并交换）
- 数据库事务里的原子性（ACID 里的 A）

------

### 三、一句话总结

**原子操作就是 “要么完整执行，要么完全不执行，绝不执行一半” 的最小操作单元，用来保证并发安全和数据一致性。**

例如mysql 悲观锁就是原子操作

## 什么是幂等

通俗理解

你可以把它理解成：

**重复操作不会产生副作用，不会把事情搞乱。**

举个生活里的例子：

- 电梯按 “关门”：按一次关门，按十次还是关门 → **幂等**
- 电梯按 “上楼”：按一次上一层，按十次上十层 → **不幂等**

在 MySQL / 接口里的意思

1）接口层面

同一个请求，调用 1 次、10 次、100 次：

- 业务结果一致
- 数据不会重复新增、重复扣款、重复扣库存

典型幂等场景：

- 支付接口
- 订单创建
- 消息重复推送
- 重试机制

2）SQL 层面

看语句本身：

**幂等 SQL**

```sql
UPDATE user SET status = 1 WHERE id = 100;
```

执行多少次，结果都是 status=1 → **幂等**

**非幂等 SQL**

```sql
UPDATE user SET money = money - 10 WHERE id = 100;
```

执行一次减 10，执行两次减 20 → **不幂等**

为什么要做幂等？

因为网络会重试、超时重发、前端重复点击、消息重复投递。

如果不做幂等：

- 重复下单
- 重复扣款
- 重复扣库存
- 数据错乱

常见幂等方案

1. **唯一索引 / 唯一约束**（最常用）
2. **分布式锁**（Redis 锁）
3. **状态机控制**（已支付就不能再支付）
4. **业务唯一号**（订单号、请求唯一 ID）
5. **悲观锁 / 乐观锁**

总结

- **幂等**：重复执行 = 只执行一次
- **不幂等**：重复执行 = 结果累加 / 错乱
- 核心目的：**防止重复操作导致数据错误**

## PHP 如何实现单例模式？

### 示例代码

```php
<?php

class DB
{
    private static $instance;

    private function __construct()
    {
        // 👇 加这一句，就能看到构造函数执行了！
        echo "✅ 构造函数执行了！！！\n";
    }

    public static function getInstance()
    {
        if (!self::$instance) {
            self::$instance = new self();
        }
        return self::$instance;
    }
}

// 测试
echo "第一次获取实例\n";
$db1 = DB::getInstance();

echo "\n第二次获取实例\n";
$db2 = DB::getInstance();

echo "\n第三次获取实例\n";
$db3 = DB::getInstance();
```

**输出**

```php
$ php demo.php
第一次获取实例
✅ 构造函数执行了！！！

第二次获取实例
    
第三次获取实例    
```

### 单例模式的核心好处（通俗好懂，结合 DB 数据库类来讲）

#### 1. 全局只能创建**唯一一个对象**

不管代码里调用多少次 `DB::getInstance()`，永远就**一个实例**，不会 new 出一堆新对象。

#### 2. **节省内存**

不用重复创建多个一模一样的对象，常驻一个就行，减少内存浪费、减轻 GC 垃圾回收压力。

#### 3. **避免重复创建数据库连接（最核心）**

如果不做单例：

每次 `new DB()` 都会**新建一条数据库连接**

请求多了会：

- 数据库连接数爆满
- MySQL 报 `Too many connections`
- 服务器卡死

单例：**全程只建 1 次连接，全程复用**，极大减轻数据库压力。

#### 4. 全局共享、统一配置

整个项目任何地方拿到的都是**同一个对象**：

- 配置只初始化一次
- 不用到处重复传参数
- 配置统一、入口统一，好维护

#### 5. 禁止随意 new，控制实例创建

```php
private function __construct(){}
```

构造函数私有，**外面不能 new**，只能走 `getInstance()`，从语法上锁住只能有一个实例。

#### 6. 适合资源类场景

除了数据库，这些全都用单例：

- Redis 连接类
- 日志类
- 配置类
- 全局缓存类
- 统一请求工具类

#### 一句话总结

单例最大好处：

**只造一个对象、只建一次资源连接、省内存、控连接数、全局统一复用。**

#### 补充：单例缺点（顺带记住）

1. 容易造成**全局变量滥用**，耦合度变高
2. 单元测试不太好 mock
3. 生命周期跟脚本一致，对象常驻内存不销毁

## PHP 的垃圾回收机制

> PHP 使用引用计数 + 循环引用检测
>
> 当引用计数为 0 时自动释放内存

### GC 是什么

**GC = Garbage Collection**，中文：**垃圾回收**

### 直观展示

> **创建变量** → 内存占用**大幅上升**
>
> **销毁变量** → 内存**立刻回落**（释放给系统）
>
> 数字肉眼可见变化，**GC 工作得非常明显**

```php
<?php

echo "初始内存：" . memory_get_usage() . "\n";

// 创建一个巨大的数组（占用大量内存）
$bigArray = array_fill(0, 100000, '这是一大段数据让内存暴涨');

echo "创建大数组后：" . memory_get_usage() . "\n";

// 销毁 → 垃圾回收立刻生效
unset($bigArray);

// 强制回收（可选，PHP 会自动回收，但加上更明显）  ps:手动触发垃圾回收
gc_collect_cycles();

echo "unset + GC 回收后：" . memory_get_usage() . "\n";
```

**结果**

```php
$ php demo.php
初始内存：405368
创建大数组后：4599784
unset + GC 回收后：405400
```



### 直观展示2

> - 第 3 步：unset 后**内存没变化**（泄漏）
> - 第 4 步：GC 一触发，**内存瞬间回落**

```php
<?php
echo "1. 初始内存：" . memory_get_usage() . "\n";

// 制造循环引用（互相引用，普通回收搞不定）
$a = [];
$b = [];
$a['other'] = &$b;
$b['other'] = &$a;

echo "2. 生成循环引用后：" . memory_get_usage() . "\n";

// 销毁外部引用
unset($a, $b);

echo "3. unset 后（内存泄漏，没回收）：" . memory_get_usage() . "\n";

// 手动触发垃圾回收
$回收数量 = gc_collect_cycles();
echo "4. 手动 GC 回收后：" . memory_get_usage() . " | 回收了 $回收数量 个垃圾对象\n";
```

**结果**

```php
$ php demo.php
1. 初始内存：406728
2. 生成循环引用后：407576
3. unset 后（内存泄漏，没回收）：407576
4. 手动 GC 回收后：406760 | 回收了 2 个垃圾对象
```





## 如何防止 SQL 注入？

- 使用 PDO 预处理
- 参数绑定
- 不拼接 SQL 字符串

❌ 错误：

```php
$sql = "SELECT * FROM user WHERE name='$name'";
```

✅ 正确：

```php
$stmt = $pdo->prepare("SELECT * FROM user WHERE name=?");
$stmt->execute([$name]);
```

## PHP 数组有哪些类型

### 1. 索引数组（数字下标）

**特点**：用**数字**作为数组的键（下标），默认从 0 开始自动分配，适合有序列表。

示例代码

```php
<?php
// 方式1：自动分配下标（0,1,2,3...）
$fruits = array("苹果", "香蕉", "橙子", "葡萄");
// 新版PHP简写
$fruits = ["苹果", "香蕉", "橙子", "葡萄"];

// 访问元素：下标从0开始
echo $fruits[0]; // 输出：苹果
echo $fruits[2]; // 输出：橙子
?>
```

### 2. 关联数组（自定义键名）

**特点**：用**字符串 / 自定义名称**作为键，适合存储「键值对」数据（比如姓名、年龄、价格）。

示例代码

```php
<?php
// 定义：键 => 值
$person = [
    "name" => "张三",
    "age"  => 25,
    "city" => "北京"
];

// 访问元素：用键名获取
echo $person["name"]; // 输出：张三
echo $person["age"];  // 输出：25
?>
```



### 3. 多维数组（数组里套数组）

示例代码

```php
<?php
// 多个用户信息（数组中包含数组）
$users = [
    [
        "name" => "张三",
        "age"  => 25
    ],
    [
        "name" => "李四",
        "age"  => 30
    ]
];

// 访问：先下标，再键名
echo $users[0]["name"]; // 输出：张三
echo $users[1]["age"];  // 输出：30
?>
```

###  总结

| 数组类型 |     键的形式     |        适用场景         |
| :------: | :--------------: | :---------------------: |
| 索引数组 | 数字（0,1,2...） |     列表、有序数据      |
| 关联数组 |   自定义字符串   |    键值对、配置信息     |
| 多维数组 |     数组嵌套     | 复杂数据（列表 + 详情） |

## laravel的生命周期

在 Laravel 里，“生命周期（Lifecycle）”通常指：**一次 HTTP 请求从进入框架到返回响应，Laravel 内部经历了哪些阶段。**

核心流程可以概括成：

```php
public/index.php
    ↓
Bootstrap（启动框架）
    ↓
Service Container（服务容器）
    ↓
Service Provider 注册/启动
    ↓
HTTP Kernel
    ↓
Middleware（中间件）
    ↓
Router（路由）
    ↓
Controller / Closure
    ↓
Response（响应）
    ↓
Terminate（结束阶段）
```

### 1. 请求入口：public/index.php

所有请求先进入：

```php
public/index.php
```

核心代码：

```php
$app = require_once __DIR__.'/../bootstrap/app.php';

$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);

$response = $kernel->handle(
    $request = Illuminate\Http\Request::capture()
)->send();

$kernel->terminate($request, $response);
```

这里干了几件事：

| 操作                | 作用                   |
| ------------------- | ---------------------- |
| bootstrap/app.php   | 创建 Laravel 应用实例  |
| make(Kernel::class) | 从容器解析 HTTP Kernel |
| handle()            | 开始处理请求           |
| send()              | 返回响应               |
| terminate()         | 执行结束逻辑           |

### 2. 创建 Application 实例

文件：

```php
bootstrap/app.php
```

核心：

```php
$app = new Illuminate\Foundation\Application(
    $_ENV['APP_BASE_PATH'] ?? dirname(__DIR__)
);
```

这里创建了：

Application

本质：

```php
Laravel Application = 服务容器 + 框架核心
```

它继承：

```php
Illuminate\Container\Container
```

所以：

Laravel 的核心其实是 IoC 容器

### 3. 绑定核心接口

接着：

```php
$app->singleton(
    Illuminate\Contracts\Http\Kernel::class,
    App\Http\Kernel::class
);
```

绑定：

| 接口              | 实现                   |
| ----------------- | ---------------------- |
| Http Kernel       | App\Http\Kernel        |
| Console Kernel    | App\Console\Kernel     |
| Exception Handler | App\Exceptions\Handler |

之后：

```php
$app->make(...)
```

就能自动解析。

### 4. Kernel 启动流程

进入：

```php
App\Http\Kernel
```

继承：

```php
Illuminate\Foundation\Http\Kernel
```

调用：

```php
handle($request)
```

内部：

```php
sendRequestThroughRouter()
```

### 5. Bootstrap 阶段

Laravel 会执行 bootstrap 数组：

```php
protected $bootstrappers = [
    LoadEnvironmentVariables::class,
    LoadConfiguration::class,
    HandleExceptions::class,
    RegisterFacades::class,
    RegisterProviders::class,
    BootProviders::class,
];
```

#### 5.1 加载 .env

```php
APP_ENV=local
DB_HOST=127.0.0.1
```

读取环境变量。

#### 5.2 加载配置

读取：

```php
config/*.php
```

生成配置仓库：

```php
config('app.name')
```

#### 5.3 异常处理注册

注册：

- error handler
- exception handler
- fatal error handler

#### 5.4 注册 Facade

例如：

```php
Cache::get()
```

本质：

```php
Facade -> Container -> Real Object
```

Facade 不是静态类。

它是：

静态代理

#### 5.5 注册 Service Provider

读取：

```php
config/app.php
```

中的：

```php
'providers' => [
]
```

然后执行：

```php
register()
```

#### 5.6 Boot Service Provider

执行：

```php
boot()
```

注意：

| 方法     | 时机     |
| -------- | -------- |
| register | 注册绑定 |
| boot     | 使用服务 |

这是面试高频题。

### 6. 服务容器解析（核心）

Laravel 最核心部分：

Service Container

例如：

```
public function __construct(UserService $service)
```

Laravel 自动：

- 反射
- 创建对象
- 注入依赖

本质：

```
ReflectionClass
```

递归解析依赖树。

### 7. Middleware 中间件阶段

Kernel：

```
protected $middleware
```

全局中间件：

例如：

```
TrimStrings
ConvertEmptyStringsToNull
StartSession
VerifyCsrfToken
```

执行方式：

```
洋葱模型
```

即：

```
before
    before
        Controller
    after
after
```

类似：

```
next($request)
```

### 8. Router 路由分发

Laravel 找匹配路由：

```
Route::get('/users', ...)
```

内部：

```
RouteCollection
```

匹配：

- URI
- Method
- Domain
- Middleware

### 9. Controller 调用

例如：

```
UserController@index
```

Laravel 会：

#### 9.1 解析控制器

通过容器：

```
$app->make(UserController::class)
```

#### 9.2 方法依赖注入

例如：

```
public function index(Request $request)
```

自动注入 Request。

### 10. 执行业务逻辑

这里才是：

- DB
- Redis
- MQ
- Cache
- Service

真正业务代码。

### 11. 返回 Response

Laravel 支持：

```
return 'hello';
return view(...);
return response()->json(...);
```

最终统一转换：

```
Symfony Response
```

### 12. Response Send

执行：

```
$response->send();
```

输出：

- header
- body
- cookies

### 13. terminate 阶段

请求结束后：

```
$kernel->terminate()
```

执行：

- terminate middleware
- session 保存
- 日志
- queue after response

例如：

```
dispatch(function () {
    //
})->afterResponse();
```

## swoole协程与线程还有进程的区别

### 核心的区别

```php
进程 = 资源隔离单位
线程 = CPU 调度单位
协程 = 用户态调度单位
```

### 最直观的理解

假设你开一家餐厅：

- **进程**：一家独立分店
- **线程**：分店里的员工
- **协程**：员工手里的待办任务切换

### 进程（Process）

操作系统级别。

每个进程：

- 有独立内存空间
- 相互隔离
- 崩一个不影响别的
- 创建成本高

PHP-FPM 就是典型多进程模型。

例如：

```bash
master
 ├── worker1
 ├── worker2
 ├── worker3
```

每个 worker 都是独立进程。

### 特点

#### 优点

- 稳定
- 隔离性强
- 不容易互相污染

#### 缺点

- 创建/切换开销大
- IPC（进程通信）复杂
- 内存占用高

### 线程（Thread）

线程属于进程内部。

一个进程可以有多个线程：

```bash
进程
 ├── 线程1
 ├── 线程2
 └── 线程3
```

线程：

- 共享内存
- 共享变量
- 切换比进程快
- 但容易线程安全问题

### 协程（Coroutine）

协程本质：

> 用户态轻量级线程

它不是操作系统调度。

而是：

```
Swoole自己调度
```

####  案例

> 协程不是并行。
>
> 是：
>
> “一个线程里的多个任务切换”

```php
go(function () {
    Co::sleep(1);
    echo "A";
});

go(function () {
    Co::sleep(1);
    echo "B";
});
```

看起来像同时执行。

实际上：

```
任务A运行
遇到IO阻塞
主动让出CPU

任务B运行
遇到IO阻塞
主动让出CPU
```

这叫：

协作式调度

### 协程为什么快？

因为：

**线程切换代价非常高**

线程切换涉及：

- 内核态切换
- 寄存器保存
- CPU cache 失效

**协程切换只在用户态**

Swoole 只需要：

- 保存栈
- 保存上下文

不需要进入内核。

所以：

```
线程：微秒级切换
协程：纳秒级切换
```