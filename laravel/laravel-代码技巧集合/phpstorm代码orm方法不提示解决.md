##  不提示原因

Laravel 的 `Model::where()` 是**魔术静态方法**，底层靠 `__callStatic` 转发，原生 PHPStorm / VSCode 无法静态分析，所以鼠标点不动、没有自动提示。

解决方案分两套：**免费开源 laravel-ide-helper（推荐）** + 临时写法兜底，区分 PHPStorm 和 VSCode。

**具体代码**

> 默认**vendor\laravel\framework\src\Illuminate\Database\Eloquent\Model.php**没有**where静态方法**

```php
   # 项目\vendor\laravel\framework\src\Illuminate\Database\Eloquent\Model.php

    /**
     * Handle dynamic static method calls into the model.  处理模型中的动态静态方法调用。
     *
     * @param  string  $method
     * @param  array  $parameters
     * @return mixed
     */
    public static function __callStatic($method, $parameters)
    {
        return (new static)->$method(...$parameters);
    }
```



### 免费方案(不推荐太麻烦)：安装 laravel-ide-helper（必做）

#### 安装依赖

```php
composer require --dev barryvdh/laravel-ide-helper
```

> Laravel9+ 自动发现服务提供者，**不需要手动改 config/app.php**

#### 依次执行三条生成命令

```php
# 生成 Facade、基础查询辅助文件 _ide_helper.php
php artisan ide-helper:generate

# 生成 phpstorm.meta.php，增强容器/链式调用识别
php artisan ide-helper:meta

# 生成模型查询构建器提示（关键！解决 where 链式无提示）
# -M 不修改模型源码，单独生成 _ide_helper_models.php
php artisan ide-helper:models -M
```

####  .gitignore 添加（不要提交辅助文件）

```php
_ide_helper.php
_ide_helper_models.php
.phpstorm.meta.php
```

#### IDE 刷新索引

- **PHPStorm**：`文件 → 无效缓存并重启`  **file->Invalidate Caches**
- **VSCode**：Ctrl+Shift+P → `Intelephense: Index workspace`

## 方案二:代码写法(推荐)

### 原理

`query()` 是模型真实存在的静态方法，IDE 能明确识别返回值 `Eloquent\Builder`，**不需要给任何模型加注释**，所有模型通用。

> 成本：只规范业务代码写法，不用改动任何 Model 文件，一万个模型也零修改。

### 代码修改

把

```php
AdminRoleModel::where(...)
```

改成加上 `query()`

```php
AdminRoleModel::query()->where(...)
```

绝大多数 IDE 立刻识别 `where/leftJoin/get/paginate`，**最简单应急手段**



