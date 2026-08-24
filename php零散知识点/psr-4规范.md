## 参考资料

[PHP PSR标准规范](https://learnku.com/docs/psr/psr-4-autoloader-example/1609)

## PSR‑4 通俗讲

PSR‑4 是 PHP 的**自动加载规范**，简单说：**命名空间 和 文件路径一一对应，不用手动 require/include，new 类的时候自动加载文件**。

> 只约束「命名空间 ↔ 文件路径」映射规则，不是 PHP 内置功能，需要 composer 来实现这套自动加载。

### 核心规则

1. 命名空间 = 目录路径，`\` 对应目录分隔符 `/`
2. 类名 = 文件名，**文件名必须和类名一模一样，大小写一致**，后缀 `.php`
3. 有一个「根命名空间前缀」映射到某个实际文件夹。

### 举个最简单例子

### 目录结构

```
src/
└── Controller/
    └── User.php
```

`User.php` 代码：

```
<?php
namespace App\Controller;

class User
{

}
```

- 命名空间：`App\Controller`
- 类：`User`
- 完整类名：`App\Controller\User`

PSR‑4 映射规则：

> 把命名空间前缀 `App\` 映射到文件夹 `src/`

完整类名拆解： `App\Controller\User`

- 前缀 `App\` → `src/`
- 剩下部分 `Controller\User` → 转路径 `Controller/User.php`

拼接得到文件路径： `src/Controller/User.php` ✔ 和实际文件对上。

### composer.json 怎么写 psr‑4

```
{
    "autoload": {
        "psr-4": {
            "App\\": "src/"
        }
    }
}
```

> ⚠️ json 里反斜杠要写两个 `\\`；结尾 `App\\` 带反斜杠，`src/` 带斜杠。

写完执行命令生成自动加载文件：

```
composer dump-autoload
```

项目入口引入 composer 的自动加载：

```
<?php
require __DIR__ . '/vendor/autoload.php';

// 直接 new，不用 require User.php，自动去找 src/Controller/User.php
$user = new \App\Controller\User();
```

### 容易踩坑点

1. **大小写敏感** Linux/Mac 下文件名区分大小写，`User.php` 和 `user.php` 是两个文件，类名文件名必须完全一致。Windows 不敏感，本地没事上线报错。
2. 命名空间前缀末尾必须带 `\` 错误：`"App": "src/"` 正确：`"App\\": "src/"`
3. 多个命名空间可以写多个

```
"psr-4": {
    "App\\": "src/",
    "Admin\\": "admin/"
}
```

1. PSR‑4 和 PSR‑0 区别

- PSR‑0：下划线 `_` 也会转成目录，老旧规范，**现在不用**
- PSR‑4：下划线不处理，现代项目全部用这个。

### 快速记忆口诀

> **命名空间前缀映射文件夹，剩下的命名空间部分当目录，类名当文件名，.php 后缀。composer 帮你自动找文件。**

### `files` 自动加载

`psr‑4`：**类自动加载**，用到类的时候才去找文件。 `files`：**强制直接加载文件**，不管你用不用，项目启动就 `require` 进来。

> 写在 `files` 数组里的文件，composer 生成 autoload 时，会把这些文件直接写进 `vendor/autoload.php`，只要你引入 `autoload.php`，这些文件立刻被执行。

### 看你配置

```
"files": [
    "app/Helpers/function.php"
]
```

含义：

> 每次项目启动，自动引入 `app/Helpers/function.php`

适合放：**全局函数、辅助函数**。 因为 PHP 的**函数没有自动加载机制**，PSR‑4 只管类，管不了普通函数。 所以辅助函数文件只能丢 `files`。

### `app/Helpers/function.php` 示例

```
<?php
// 这里不能写类，写一堆全局辅助函数
function hello()
{
    return '你好';
}

function format_money($num)
{
    return number_format($num,2);
}
```

之后项目任何地方，**不用 include，不用 use**，直接调用：

```
echo hello();
echo format_money(100);
```

### 和 psr‑4 对比

表格

| 类型  | 作用         | 触发时机                 | 适合放什么         |
| ----- | ------------ | ------------------------ | ------------------ |
| psr‑4 | 类自动加载   | `new 类` /use 类时才加载 | class 类文件       |
| files | 强制引入文件 | 项目一启动就加载         | 全局函数、常量文件 |

### ⚠️注意坑

1. `files` 里面的文件**每次请求都会加载**，不要把类文件丢这里，会浪费性能。类一律交给 psr‑4。
2. 修改 `files` 配置后，**必须执行**

```
composer dump-autoload
```

不然不生效！很多人忘记这一步，函数找不到。 3. 路径是相对于 `composer.json` 文件所在目录。

### 补充还有一个 `classmap`

很少用：

```
"classmap": [
    "lib/"
]
```

扫描目录，把里面所有类名和文件路径生成一张映射表，就算不遵守 psr‑4 的老类也能自动加载。Laravel 基本不用。

------

简单总结：

> psr‑4 管**类**；files 管**全局函数文件**，启动就加载。改完 files 记得 `composer dump-autoload`。