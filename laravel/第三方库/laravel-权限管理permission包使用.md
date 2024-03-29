# 说明&资料

##  说明

> 实际项目开发中我们经常会遇到对用户的某个权限进行管理比如
>
> 超级管理员拥有删除文章的权限，而普通会员只有查阅的权限，
>
> 自己搭建一个rbac权限服务很耗费时间精力，没有必要去造轮子

## 资料

| name                                 | linkUrl                                                      |
| ------------------------------------ | ------------------------------------------------------------ |
| 官方使用文档                         | [link](https://spatie.be/docs/laravel-permission/v4/introduction) |
| github项目地址                       | [link](https://github.com/spatie/laravel-permission)         |
| packagist                            | [link](https://packagist.org/packages/spatie/laravel-permission) |
| 后盾人                               | [link](https://doc.houdunren.com/%E6%8F%92%E4%BB%B6%E6%89%A9%E5%B1%95/%E5%90%8E%E5%8F%B0%E6%8F%92%E4%BB%B6/2%20permission.html) |
| laravel-学院（推荐看这个比较好理解） | [link](https://learnku.com/articles/9842/user-role-permission-control-package-laravel-permission-usage-description) |
| 原生-rbac权限管理                    | [link](https://www.cnblogs.com/yaoliuyang/p/12710798.html)   |

## 安装

**通过composer安装**

> 在项目根目录执行

```shell
composer require spatie/laravel-permission
```

- 发布配置文件

```shell
# 发布配置文件 此命令用于生成  config\permission.php
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider" --tag="config"
```

- 发布迁移文件

```shell
# 生成迁移文件，根据业务需要可以随意添加表字段
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider" --tag="migrations"
```

- 生成迁移文件，根据业务需要可以随意添加表字段

```shell
php artisan migrate
# 上述迁移执行之后会在数据库中生成如下表
roles # 角色表
permissions # 权限表
role_has_permissions # 角色权限表
model_has_roles # 用户角色表，用户通过角色获取权限使用
model_has_permissions # 用户权限表，用户直接获取权限使用
```

**对应数据表**

```shell
# roles 角色表
CREATE TABLE `roles` (
  `id` bigint unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `guard_name` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `created_at` timestamp NULL DEFAULT NULL,
  `updated_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `roles_name_guard_name_unique` (`name`,`guard_name`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

# permissions 权限表
CREATE TABLE `permissions` (
  `id` bigint unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `guard_name` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `created_at` timestamp NULL DEFAULT NULL,
  `updated_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `permissions_name_guard_name_unique` (`name`,`guard_name`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
# 角色权限表
CREATE TABLE `role_has_permissions` (
  `permission_id` bigint unsigned NOT NULL,
  `role_id` bigint unsigned NOT NULL,
  PRIMARY KEY (`permission_id`,`role_id`),
  KEY `role_has_permissions_role_id_foreign` (`role_id`),
  CONSTRAINT `role_has_permissions_permission_id_foreign` FOREIGN KEY (`permission_id`) REFERENCES `permissions` (`id`) ON DELETE CASCADE,
  CONSTRAINT `role_has_permissions_role_id_foreign` FOREIGN KEY (`role_id`) REFERENCES `roles` (`id`) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

# 用户角色表，用户通过角色获取权限使用
CREATE TABLE `model_has_roles` (
  `role_id` bigint unsigned NOT NULL,
  `model_type` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `model_id` bigint unsigned NOT NULL,
  PRIMARY KEY (`role_id`,`model_id`,`model_type`),
  KEY `model_has_roles_model_id_model_type_index` (`model_id`,`model_type`),
  CONSTRAINT `model_has_roles_role_id_foreign` FOREIGN KEY (`role_id`) REFERENCES `roles` (`id`) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

# 用户权限表，用户直接获取权限使用
CREATE TABLE `model_has_permissions` (
  `permission_id` bigint unsigned NOT NULL,
  `model_type` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `model_id` bigint unsigned NOT NULL,
  PRIMARY KEY (`permission_id`,`model_id`,`model_type`),
  KEY `model_has_permissions_model_id_model_type_index` (`model_id`,`model_type`),
  CONSTRAINT `model_has_permissions_permission_id_foreign` FOREIGN KEY (`permission_id`) REFERENCES `permissions` (`id`) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

# 使用

首先需要在对应需要设置权限的模型中添加`HasRoles` trait和设置 `$guard_name` 属性

```php
use Spatie\Permission\Traits\HasRoles;

class User extends Authenticatable
{
    use HasRoles;
    protected $guard_name = ['sanctum'];
    ...
}
```

## 常用代码示例

```php
<?php

namespace App\Http\Controllers;


use App\Models\User;
use Spatie\Permission\Models\Permission;
use Spatie\Permission\Models\Role;

class TestController extends Controller
{
    public function index()
    {
        # 创建角色 && 权限
        $role = Role::create(['name' => '司令']);#创建一个司令角色
        $permission = Permission::create(['name' => '胡作非为','cn_name'=>'']);#添加权限
        $user = User::find(1);
        $user->assignRole("司令");#给用户分配权限
        $user->givePermissionTo('胡作非为');#给用户分配权限
        Permission::all();#得到全部的权限 
        $user->hasPermissionTo('胡作非为');#判断用户是否拥有一个权限
        # 向用户添加权限
        $user->givePermissionTo('edit articles');
        # 通过角色添加权限
        $user->assignRole('writer');
        $role->givePermissionTo('edit articles');
        # 因为所有的权限都会在Laravel 的 gatecan上注册，所以你可以通过 Laravel 的默认函数来检查用户是否有权限：
        $user->can('edit articles');
    }
}
```

## blade模板鉴权(现以分离开发 废弃)

Blade 和角色

测试一个特定的角色：

```php
@role('writer')
    I am a writer!
@else
    I am not a writer...
@endrole
    
```

同上

```php
@hasrole('writer')
    I am a writer!
@else
    I am not a writer...
@endhasrole
```

测试列表中的任何角色：

```php
@hasanyrole($collectionOfRoles)
    I have one or more of these roles!
@else
    I have none of these roles...
@endhasanyrole
// or
@hasanyrole('writer|admin')
    I am either a writer or an admin or both!
@else
    I have none of these roles...
@endhasanyrole
```

测试所有角色：

```php
@hasallroles($collectionOfRoles)
    I have all of these roles!
@else
    I do not have all of these roles...
@endhasallroles
// or
@hasallroles('writer|admin')
    I am both a writer and an admin!
@else
    I do not have all of these roles...
@endhasallroles
```

[#](https://doc.houdunren.com/插件扩展/后台插件/2 permission.html#blade-和权限)Blade 和权限

此软件包不会添加任何特定于权限的 Blade 指令。 因此，使用 Laravel 原生的 `@can` 指令来检查用户是否具有某种权限。

```php
@can('edit articles')
  //
@endcan
```

or

```php
@if(auth()->user()->can('edit articles') && $some_other_condition)
  //
@endif
```

#  实战讲解示例

## 创建权限

> 利用**[laravel-数据填充](https://learnku.com/docs/laravel/8.x/seeding/9404)** 功能填充对应的权限

```shell
# 这里只简单的演示添加权限数据信息,例如用户创建角色什么的可以自行添加

# 创建填充类
php artisan make:seeder CreatePermission

....
public function run()
    {
       
        // 重置角色和权限缓存
        app()['cache']->forget('spatie.permission.cache'); # 清理缓存

        // 创建权限
        Permission::create(['name' => 'auth.login','guard_name'=>'api','cn_name'=>'用户登录']); # 用户登录权限
        Permission::create(['name' => 'order.goods','guard_name'=>'api','cn_name'=>'商品列表']);
        ...

        // 创建角色并分配创建的权限

        $role = Role::create(['name' => 'super-admin','cn_name'=>'超级管理员']);
        .....

        $role = Role::create(['name' => 'moderator']);
        # 给角色授权
        $role->givePermissionTo(['auth.login', 'order.goods']);# 一个一个写入权限(不推荐) 
        $role->givePermissionTo(Permission::all()); # 全部默认写入
    }
.......

# 用户角色表等等等等(记得用户表默认创建一个超级管理员用户也是填充添加)

# 最后 利用 数据填充自带的 DatabaseSeeder表统一添加填充
/**
 * 执行数据库填充
 *
 * @return void
 */
public function run()
{
    $this->call([
        CreatePermission::class,
        Xxxxx::class,
        Xxxxx::class,
    ]);
}
```





## 鉴权中间件

> 记得**注册中间件**

```shell
# 创建中间件
php artisan make:middleware CheckPermission

# handle方法中添加返回 
public function handle(Request $request, Closure $next)
    {
        $userModel= User::find(id); # 这里写死我们可以用登录校验中间件获取用户实例;
        $request_url_name=$request->route()->getName(); # 获取当前路由请求名称 即  ->name('xxx.xxx')
        if (!$userModel->can($request_url_name)){  #记得模型中添加 use HasRoles; 后可以使用can方法校验是否拥有权限 
            return response()->json(['code'=>403,'message'=>'no_permission']);
        }
        return $next($request);
    }
```





```shell
//用户登录
Route::group(['prefix' => 'auth'], function () {
    Route::any('register', [\App\Http\Controllers\UserAuthController::class, 'register'])->name('auth.register'); //用户注册
    Route::any('login', [\App\Http\Controllers\UserAuthController::class, 'login'])->name('auth.login'); //用户登录
    Route::get('test', [\App\Http\Controllers\UserAuthController::class, 'test'])->name('auth.test')->middleware('auth:api'); //测试jwt路由
});
# 商品相关路由
  Route::group(['prefix' => 'order', ['middleware' => ['auth:api', 'bind']]], function ($api) {
    //分类列表
    Route::get('category', [\App\Http\Controllers\CategoryController::class,'index'])->name('order.category');
    // 商品列表
    Route::get('goods', [\App\Http\Controllers\GoodsController::class,'list'])->name('order.goods');
});
```













