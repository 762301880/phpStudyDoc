## laravel原生情况下防注入

### 你日常最常见的危险写法（项目遍地都是）

> 危险一想让他报错很简单 传递参数里面**'** 符号就可以让他报错 
>
> ```json
> {
> 
>   "name":"'02"
> }
> ```
>
> 

```php
// 危险 1：raw 直接塞变量
$key = $request->key;
User::whereRaw("name like '%{$key}%'")->get();   # 想让这个语句报错很简单 例如传递参数 

// 危险 2：动态排序、前端传字段
$sort = $request->sort;
User::orderByRaw($sort)->get();

// 危险 3：in 条件偷懒拼接
$ids = [1,2,3];
$str = implode(',', $ids);
DB::table('a')->whereRaw("id in ({$str})");

// 危险 4：多条件复杂业务、原生SQL片段拼接
$start = $request->start_time;
DB::table('log')->whereRaw("create_time >= '{$start}'");
```

### 原生表达式预处理

> [官方处理参考](https://docs.golaravel.com/docs/9.x/queries#raw-methods)  
>
> [laravel学院](https://learnku.com/docs/laravel/8.x/queries/9401#3446b9)

Laravel 官方文档（中文 + 英文）明确写了

1）中文文档（查询构造器 → raw 方法）

地址：https://docs.golaravel.com/docs/9.x/queries#raw-methods

原文直接写：

> `whereRaw` 和 `orWhereRaw` 方法将原生的「where」注入到你的查询中。这两个方法的**第二个参数是可选项，值是一个绑定参数的数组**。

我直接把你这 **4 段危险代码全部改成 100% 安全的预处理写法**，

**全部使用？占位符 + 参数绑定，无任何拼接，可直接复制到项目里用。**

**最终全部安全版汇总**

```php
// 1. 模糊查询
User::whereRaw("name like ?", ["%{$key}%"])->get();

// 2. 动态排序
$allowFields = ['id', 'name', 'created_at'];
$sort = in_array($request->sort, $allowFields) ? $request->sort : 'id';
User::orderBy($sort)->get();

// 3. IN 查询
DB::table('a')->whereRaw("id in (?)", [$ids])->get();

// 4. 时间条件
DB::table('log')->whereRaw("create_time >= ?", [$start])->get();
```

### 核心规则（记住这个就永远不会写出注入漏洞）

1. **值** → 用 `?` + 参数数组
2. **字段 / 表名 / 排序** → 用白名单
3. **永远不要把变量直接写进 SQL 字符串里**



##  原生sql防注入

> 原生防注入只有 1 个终极方案
>
> **PDO 预处理 + ? 占位符 + 参数绑定**
>
> 这是 **全世界通用、唯一 100% 安全** 的方法。

### 原生完整代码

**连接数据库**

```php
$host = '127.0.0.1';
$dbname = '你的库名';
$username = 'root';
$password = '你的密码';

// 连接
$pdo = new PDO("mysql:host=$host;dbname=$dbname;charset=utf8mb4", $username, $password);
// 关键：关闭模拟预处理（防注入必须加）
$pdo->setAttribute(PDO::ATTR_EMULATE_PREPARES, false);
```

**防注入查询（核心！）**

```php
// 用户输入
$name = $_POST['name'];
$age = $_POST['age'];

// 1. 写 SQL，变量用 ? 代替
$stmt = $pdo->prepare("SELECT * FROM user WHERE name = ? AND age = ?");

// 2. 把变量丢进数组，执行
$stmt->execute([$name, $age]);

// 3. 获取结果
$user = $stmt->fetch();
```

### 1. 模糊查询

```php
// 危险写法（拼接）
// $sql = "SELECT * FROM user WHERE name LIKE '%$name%'";

// 安全写法
$stmt = $pdo->prepare("SELECT * FROM user WHERE name LIKE ?");
$stmt->execute(["%$name%"]);
```

### 2. IN 查询

```php
$ids = [1,2,3];

// 安全写法
$stmt = $pdo->prepare("SELECT * FROM a WHERE id IN (?,?,?)");
$stmt->execute($ids);
```

### 3. 时间条件

```php
$start = $_POST['start_time'];

$stmt = $pdo->prepare("SELECT * FROM log WHERE create_time >= ?");
$stmt->execute([$start]);
```

### 4. 动态排序（白名单）

```php
$allow = ['id','name','create_time'];
$sort = in_array($_POST['sort'], $allow) ? $_POST['sort'] : 'id';

// 排序字段不能用?，只能白名单
$sql = "SELECT * FROM user ORDER BY $sort";
```

