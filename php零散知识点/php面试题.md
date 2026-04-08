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

