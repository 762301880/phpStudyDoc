# 一、资料&说明



## 1.1 资料

| name                   | url                                                          |
| ---------------------- | ------------------------------------------------------------ |
| github-Eloquent Filter | [链接](https://github.com/Tucker-Eric/EloquentFilter)        |
| 第三方博客参考         | [链接](https://www.itxwzj.com/technology/14/eloquent-conditional-query-code-separation-in-laravel) |



# 二、安装&使用

## 2.1 安装

- 使用composer安装

```shell
composer require tucker-eric/eloquentfilter
```

- `发布配置文件`

```shell
php artisan vendor:publish --provider="EloquentFilter\ServiceProvider"
```

- `在`config/eloquentfilter.php`配置文件中设置模型过滤器将驻留的名称空间：`

```shell
'namespace' => "App\\ModelFilters\\",
```

## 2.2 使用

### 2.2.1 生成filter模型类

- 使用artisan 命令

  ```shell
  php artisan model:filter +你的模型过滤器名称  # 此命令会在`App\ModelFilters目录下为你生成一个过滤逻辑文件
  ```

  - 例子

  ```php
  例：php artisan model:filter UserFilter
  <?php 
  namespace App\ModelFilters;
  
  use EloquentFilter\ModelFilter;
  
  class UserFilter extends ModelFilter
  {
      /**
      * Related Models that have ModelFilters as well as the method on the ModelFilter
      * As [relationMethod => [input_key1, input_key2]].
      *
      * @var array
      */
      public $relations = [];
  }
  ```

  

- 如果要使用查询过滤需要在对应的模型中声明 use Filterable;

 ```php
 <?php
 
 namespace App;
 
 use EloquentFilter\Filterable;
 use Illuminate\Database\Eloquent\Model;
 
 class User extends Model
 {
     use Filterable;# 引用 trait
 }
 ```

## 2.4 常用方法

````shell
public function setup() # 此方法中的逻辑会优先处理
{
 if (!empty($this->input('start_time')) && !empty($this->input('end_time'))) { # 例如我们可以增加逻辑查询
            //注意拼接的时间格式一定要加上空格例子' 00:00:00'
      $this->whereBetween('created_at', [$this->input('start_time') . ' 00:00:00', $this->input('end_time') . ' 23:59:59']);
  }
  # 排序等操作 
  $this->orderBy('---');
  # 关联查询
  $this->leftJoin('---');
  # 选择字段
  $this->select('---')
        );
}
````



# 注意事项

## 注意如果项目中`filter`没有提示：请使用`composer require barryvdh/laravel-ide-helper`插件

 

