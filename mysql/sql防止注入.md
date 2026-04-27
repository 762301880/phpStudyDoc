## laravel原生情况下防注入

### 你日常最常见的危险写法（项目遍地都是）

```php
// 危险 1：raw 直接塞变量
$key = $request->key;
User::whereRaw("name like '%{$key}%'")->get();

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

