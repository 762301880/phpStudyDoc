## 一、底层核心：操作系统文件系统差异

### 1. Windows（本地开发）

Windows NTFS 文件系统**大小写不敏感，但保留大小写**

- `/Controller` 和 `/controller` 视为同一个文件夹

  ```
  User.php
  ```

   和 

  ```
  user.php
  ```

   识别为同一个文件

  

  所以本地开发时，大小写写错不会报错。

### 2. Linux（线上服务器）

Linux 默认 ext4/xfs 等文件系统**大小写完全敏感**

- `/Controller` 和 `/controller` 是两个完全独立目录

  ```
  User.php
  ```

  ```
  user.php
  ```

  ```
  USER.php
  ```

   是三个不同文件

  

  文件查找时会精确匹配大小写，找不到直接抛出 404

  ```
  Class Not Found
  ```

## 二、Laravel 自身机制放大这个问题

### 1. Composer PSR-4 自动加载规范（最关键）

Laravel 依赖 Composer 自动加载，遵循 PSR-4：

> 命名空间、类名必须和**文件目录、文件名大小写完全一致**

示例：

```php
// 命名空间+类名（大驼峰）
namespace App\Http\Controllers;
class UserController{}
```

对应文件必须：

```
app/Http/Controllers/UserController.php
```

- 本地 Windows：写成 `usercontroller.php` 也能加载
- Linux：文件名小写直接找不到类，报致命错误

### 2. Laravel 路由、视图、资源文件读取逻辑

1. 视图文件

   

   ```
   view('user.index')
   ```

    底层去查找 

   ```
   resources/views/user/index.blade.php
   ```

   

   Linux 下 

   ```
   User/index.blade.php
   ```

    和 

   ```
   user/index.blade.php
   ```

    不是同一个文件。

2. 静态资源（css/js/img）

   

   前端引入 

   ```
   /img/banner.png
   ```

   ，服务器如果实际是 

   ```
   IMG/Banner.PNG
   ```

    会 404。

3. 目录引用、模板 include

   

   ```
   @include('Admin.layout')
   ```

    目录大小写写错直接加载失败。

### 3. Linux 环境变量、命令行工具区分大小写

Artisan 命令、自定义脚本、软链接目录全部严格区分大小写；

Windows CMD/PowerShell 忽略大小写，开发很难发现问题。

## 三、常见踩坑场景

1. 本地创建 `Controllers/user.php`，类名 `User`，Windows 正常，Linux 500 报错
2. 路由写 `Admin::class`，文件目录是 `admin/`
3. 静态图片文件夹 `Upload`，前端写 `/upload/` 访问 404
4. 数据库迁移文件名大小写不一致，`php artisan migrate` 找不到迁移类

## 四、解决方案

### 1. 开发规范（治本）

统一遵循 Laravel 标准大小写规范：

- 控制器、模型、服务：**大驼峰** `UserController.php`
- 文件夹、视图子目录：**小写下划线** `admin/user/`
- 资源目录统一小写：`public/upload/`

### 2. Windows 本地模拟 Linux 严格大小写（提前排错）

1. WSL2 开发（推荐，完全复刻 Linux 文件系统）
2. Git 配置区分文件名大小写

```bash
git config core.ignorecase false
```

1. PHPStorm 开启文件大小写校验

### 3. 服务器临时兼容（不推荐，治标）

不建议修改系统文件系统，会引发更多兼容性问题，优先规范代码。

## 总结

1. 根本原因：**Linux 文件系统大小写敏感，Windows 不敏感**；
2. 报错放大原因：Laravel PSR-4 自动加载要求类名与文件大小写强绑定；
3. 最佳实践：全程统一大小写规范，本地用 WSL 开发提前规避线上报错。