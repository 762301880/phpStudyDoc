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

```php
class DB {
    private static $instance;

    private function __construct() {}

    public static function getInstance() {
        if (!self::$instance) {
            self::$instance = new self();
        }
        return self::$instance;
    }
}
```

## PHP 的垃圾回收机制

PHP 使用引用计数 + 循环引用检测

当引用计数为 0 时自动释放内存

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

索引数组

关联数组

多维数组