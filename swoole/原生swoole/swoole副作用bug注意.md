# Swoole 的“副作用”（很多人踩坑）

## ⚠️ 1. 内存泄漏风险

因为进程不重启：

- static 变量会累积
- 单例对象不会释放
- 框架代码可能有“请求残留状态”

## static变量会累积

**示例代码hyperf中请求**

```php
static $users = [];

 // 模拟每个请求的参数
 $name = $_GET['name'] ?? 'guest';
 $users[] = $name;

 return ['users' => $users];
```

**模拟请求**

注意：默认 Hyperf 启动时会有 1~4 个 worker 进程，每个 worker 都有自己的静态变量。为了观察最明显的累积效果，可以设置 **worker_num=1**，这样所有请求都在同一个 worker 里。

**修改为一个work**

```php
# 修改 config/autoload/server.php：
'server' => [
    'worker_num' => 1,
],
```

**模拟请求**

```php
# 终端1
curl "http://127.0.0.1:9501/user/add?name=A"

# 终端2
curl "http://127.0.0.1:9501/user/add?name=B"
    
# 第一次请求终端1：
    
{"users":["A"]}

# 第二次请求终端2（同一 worker）：
{"users":["A","B"]}
```



## ⚠️ 2. Laravel/ThinkPHP 需要适配

不能直接“开箱即用”，需要：

- swoole-laravel bridge 或 hyperf
- 或重新设计生命周期

------

## ⚠️ 3. 调试复杂

- FPM：请求=一次执行
- Swoole：需要模拟并发 + 长生命周期