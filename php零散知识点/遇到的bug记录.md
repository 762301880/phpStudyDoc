## 对象未重复新建 重复调用



**看以下代码示例**

> 我的想法是   依赖注入直接一次把模型给赋值调用岂不是更方便   结果 循环里 调用 create方法 
>
> 发现重复的返回了 同一个id

```php
class ContactService extends CommonService
{
    protected $contactModel = null;

    public function __construct()
    {
        $this->contactModel = new ContactModel();
    }

    public function create($data, $return_id = false, $isFollowLog = true)
    {
        $contactModel = $this->contactModel;
        //....忽略字段
        $bool = $contactModel->save();
        if ($return_id) return $contactModel->id;  //这里直接返回了 id
        return $bool;
    }
}
```

**bug原因**

 问题的核心在于：
 **重复调用 `create()` 返回了同一个 ID** 

这通常说明 `$contactModel` **是同一个对象实例在重复使用**，没有在每次创建时重新实例化，导致第二次 `save()` 

直接更新了上一次保存的记录，而不是新建一条记录。

### 问题原因

在 `__construct()` 里写了：

```
$this->contactModel = new ContactModel();
```

这意味着 `$this->contactModel` 在整个 `ContactService` 生命周期中**只会实例化一次**。
所以当重复调用：

```
$this->create($data);
$this->create($data);
```

时，第二次 `$contactModel->save()` 实际上是**更新同一个对象**，而不是新建。
于是返回的 `$contactModel->id` 永远是第一次创建的 ID。

------

### ✅ 解决办法 1：每次创建新对象

在 `create()` 方法内部重新实例化 `ContactModel`：

```
public function create($data, $return_id = false, $isFollowLog = true)
{
    $contactModel = new ContactModel(); // ✅ 每次新建
    $contactModel->save($data);

    if ($return_id) {
        return $contactModel->id;
    }
    return true;
}
```

> 🔸这样每次调用 `create()` 都会生成新的记录，返回不同的 ID。

------

### ✅ 解决办法 2：复用时重置模型

如果你确实想复用 `$this->contactModel`（例如性能考虑），那你需要在保存前重置模型的状态：

```
public function create($data, $return_id = false, $isFollowLog = true)
{
    $this->contactModel->data([]); // 清空上次数据
    $this->contactModel->isUpdate(false); // 标记为新增

    $this->contactModel->save($data);

    if ($return_id) {
        return $this->contactModel->id;
    }
    return true;
}
```

------

### 🔧 建议

一般来说，像 `ContactService` 这种 Service 层不应该持有长期存在的模型实例，
 每次 `create()` 都 `new` 一个是最安全、最清晰的写法。

##  数据 为0 要有效 为""空要无效如何判断

```php
<?php

$value = 0;
if ($value !== "") {
    echo "Not empty";
} else {
    echo "Empty";
}

```

```php
# 这才是最优解 如果传递过来为""        就不会走查询   如果传递 0就走查询
$is_trans = $queryData['is_trans'] ?? null;
if ($is_trans !== '' && $is_trans !== null) {
    $query->where('coo.is_trans', $is_trans);
}
```

## 本地环境总是过几秒才能返回最新的接口排查

**原因**

> 我用phpstudy搭建的 laravel项目  
>
> 本地域名为 www.cs.com  
>
> 然后用postman 请求 接口
> dd(15);
>
> 每次都加一后请求 例如上次是14  这次+1 后是十五 postman要好几秒之后才会返回新的数字

经过测试 如果用**php artisan serv**启动项目 ip:8080 请求接口 没有这个问题,但是请求**phpstudy** 虚拟域名

的接口就会产生这个问题 接口总是过几秒才会返回最新的

八成不是 Laravel 代码执行慢，而是 **PHP OPcache / phpstudy 的缓存 / FPM 进程没及时刷新代码** 导致的。

很典型：

- 代码里 `dd(14)`
- 改成 `dd(15)`
- 立刻用 Postman 请求
- 结果还返回 `14`
- 等几秒后才变成 `15`

说明请求打到的还是“旧代码”。

检查 opcache

### 先看你phpstudy 是哪种运行方式

如果你是：

- Nginx + PHP-CGI / PHP-FPM
- Apache + PHP module

都可能受 opcode cache 影响。

尤其 phpstudy 默认很多 PHP 版本会开：

```bash
opcache.enable=1
```

## 1. 检查 opcache

创建一个 `phpinfo.php`

```bash
<?php
phpinfo();
```

访问：

```bash
http://www.cs.com/phpinfo.php
```

搜索：

```bash
opcache
```

如果看到：

```bash
opcache.enable => On
```

再看：

```bash
opcache.revalidate_freq
```

如果是：

```bash
2
```

或者更大：

表示 PHP 每隔几秒才检查文件变更。

你改代码后，它不会立刻重新编译。

## 2. 开发环境直接关闭 opcache（最省事）

找到 phpstudy 对应 PHP 版本的：

```
php.ini
```

修改：

```
opcache.enable=0
opcache.enable_cli=0
```

或者至少改成：

```
opcache.validate_timestamps=1
opcache.revalidate_freq=0
```

解释：

- `validate_timestamps=1` → 检查文件更新时间
- `revalidate_freq=0` → 每次请求都检查

开发环境推荐直接这样。

------

## 3. 重启 phpstudy 服务

改完后：

- 重启 nginx/apache
- 重启 php 服务

phpstudy 面板里直接重启。

不重启等于白改。

## 4. Laravel 自己的缓存也检查一下

虽然 `dd()` 一般不会受 Laravel cache 影响，但顺手清掉：

```
php artisan optimize:clear
```

它会清：

- route cache
- config cache
- view cache
- event cache