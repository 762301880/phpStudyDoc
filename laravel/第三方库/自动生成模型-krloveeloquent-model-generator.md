# 一、资料&说明

## 1.1 资料

| name      | url                                                          |
| --------- | ------------------------------------------------------------ |
| packagist | [link](https://packagist.org/packages/krlove/eloquent-model-generator) |
| github    | [link](https://github.com/krlove/eloquent-model-generator)   |

## 1.2 说明

> Eloquent Model Generator 是一个基于[Code Generator](https://github.com/krlove/code-generator)的工具，用于生成 Eloquent 模型。

# 二、安装&使用

## 2.1 安装

> 使用composer在项目的根目录执行

```shell
composer require krlove/eloquent-model-generator
```

- 如果开发环境请执行

```shell
composer require krlove/eloquent-model-generator --dev
```

## 2.2 配置

- 安装完成之后配置服务提供者

> 在`config\app.php`中的`providers`中添加
>
> 如果您使用的是 Laravel 5.5 或更高版本，此步骤可以省略，因为该项目支持[包发现](https://laravel.com/docs/5.5/packages#package-discovery)功能。

```shell
'providers' => [
    // ...
    Krlove\EloquentModelGenerator\Provider\GeneratorServiceProvider::class,
];
```



## 2.3 使用

- 指定表明生成模型

  > 在这种情况下，生成的模型将包含`protected $table = 'user'`属性。

```php
php artisan krlove:generate:model 需要生成的模型名称  --table-name=指定表名称
```

- 输出路径生成

> 1. --output-path  可以是绝对路径或相对于项目`app`目录。绝对路径必须以`/`：
> 2. --namespace  需要生成模型的保存位置

```shell
php artisan krlove:generate:model User   --output-path=./Models/ --namespace=App\\Models
```

<img src="https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/5Goi7sgjt1IVBUu.png" alt="1625792623.jpg" style="zoom:50%;" />

### 2.3.1 全局覆盖默认选项

>  无需每次执行命令时都指定选项，您可以使用您自己的默认值创建一个`eloquent_model_generator.php`在项目`config`目录中命名的配置文件。Generator 已经包含自己的配置文件，`Resources/config.php`其中包含以下选项：

`您可以通过`model_defaults`在`eloquent_model_generator.php`以下位置定义数组来覆盖它们：`

```php
<?php
return [
    'model_defaults' => [
        'namespace' => 'App\\Models', # 指定空间
        'base_class_name' => \Illuminate\Database\Eloquent\Model::class, # 指定需要继承的模型名称
        'output_path' => './Models', # 指定需要输出的模型位置
        'no_timestamps' => false, # 是否禁用时间戳
        'date_format' => null, # z
        'connection' => null,
        'backup' => true,
    ],
    'db_types' => [
        'enum' => 'string',
    ],
];
```



# 注意事项

## 1.1 如果安装完成之后下划线无法找到类

请用 [ide-helper](https://www.cnblogs.com/yaoliuyang/p/13223545.html)重新生成一下

```shell
php artisan ide-helper:generate  
```

## 1.2 如果使用命令生成报以下错误

> 这意味着您必须`<ANY_TYPE>`使用 Doctrine注册您的类型。

```shell
  Doctrine\DBAL\Exception 
  # 未知的数据库类型枚举请求，Doctrine\DBAL\Platforms\MySQL57Platform可能不支持它。
  Unknown database type enum requested, Doctrine\DBAL\Platforms\MySQL57Platform may not support it.
```

### 1.2.1 解决方案

`请在config`中手动创建`eloquent_model_generator.php`文件

> 例如，您将注册`enum`类型并希望 Doctrine 将其视为`string`（您可以[在此处](http://doctrine-orm.readthedocs.io/projects/doctrine-dbal/en/latest/reference/types.html#mapping-matrix)找到所有现有的 Doctrine 类型）。在您的添加下一行`config/eloquent_model_generator.php`：

```shell
return [
    // ...
    'db_types' => [
        'enum' => 'string',
    ],
];
```

