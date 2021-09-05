# 一、说明&资料

## 1.1 说明

> 实际项目开发中我们经常会遇到对用户的某个权限进行管理比如
>
> 超级管理员拥有删除文章的权限，而普通会员只有查阅的权限，
>
> 自己搭建一个rbac权限服务很耗费时间精力，没有必要去造轮子

## 1.2 资料

| name           | linkUrl                                                      |
| -------------- | ------------------------------------------------------------ |
| 官方使用文档   | [link](https://spatie.be/docs/laravel-permission/v4/introduction) |
| github项目地址 | [link](https://github.com/spatie/laravel-permission)         |
| packagist      | [link](https://packagist.org/packages/spatie/laravel-permission) |
| 后盾人         | [link](https://doc.houdunren.com/%E6%8F%92%E4%BB%B6%E6%89%A9%E5%B1%95/%E5%90%8E%E5%8F%B0%E6%8F%92%E4%BB%B6/2%20permission.html) |
| laravel-学院   | [link](https://learnku.com/articles/9842/user-role-permission-control-package-laravel-permission-usage-description) |

## 二、安装&使用

## 2.1 composer安装

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
model_has-permissions # 用户权限表，用户直接获取权限使用
```



## 2.2 使用





### blade模板鉴权

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