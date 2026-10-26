## 前置依赖

php8.2.9 版本

composer 2.10.3版本

## 安装

### 安装 Laravel 安装器

```bash
composer global require laravel/installer
```

### 创建应用

```bash
laravel new example-app
```

## bug解析

**composer install 安装失败报错 Root composer.json requires laravel/tinker ^2.10.1**

> 根本原因是你的 Composer 配置了阿里云镜像，且该镜像被识别为高优先级（canonical）仓库。镜像里 `laravel/tinker` 只有 `2.x-dev` 这类开发版本，不满足你项目默认的 `minimum-stability: stable`，同时高优先级仓库又遮蔽了 packagist 上的稳定版，所以报错。

**首选：移除或禁用阿里云镜像，改用官方源**

```php
composer config --unset repos.packagist
```

如果之前是用全局命令配置的镜像，再执行：

```bash
composer config --global --unset repos.packagist
```

然后重新运行 `composer update`。这样 `laravel/tinker` 的稳定版 `v2.10.1` 等就能正常从 packagist 解析。