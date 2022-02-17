# 叙述

> 今天用thinkphp写项目的时候总是发现无法打印sql语句最终找到了解决方案			

[**参考示例**](https://learnku.com/articles/38153)

# 代码示例

```shell
 # 使用buildSql()函数构建sql语句
 return Stu::where('id',2)->buildSql();
```

# [sql监听](https://www.php.cn/phpkj/thinkphp/469666.html)

```php
# 在helpers.php 助手函数中添加监听,
# 记得同步在 composer.json 中 的 autoload中添加,最后执行 composer 生成自动加载,执行命令 composer dump-autoload
"autoload": {
    "files": [
      "app/helpers.php"
    ]
  },

# helpers.php中添加
<?php

if (!file_exists('getLastSql')) {
    function getLastSql()
    {
        \think\facade\Db::listen(function($sql, $time, $explain){
            // 记录SQL
            echo $sql. "&nbsp &nbsp &nbsp &nbsp".' ['.$time.'s]';
            // 查看性能分析结果
            echo "<br><br>".$explain;
        });
    }
}
# 代码中使用示例
  getLastSql();
  $data=Stu::leftJoin('class_s','class_s.id=stu.class_id')->select();
  dd($data->toArray());

# 显示打印结果
CONNECT:[ UseTime:1.144147s ] mysql:host=81.69.231.252;port=3307;dbname=laravel_study;charset=utf8 [1,645,089,922.679819s]

SHOW FULL COLUMNS FROM `stu` [0.064779s]

SELECT * FROM `stu` LEFT JOIN `class_s` ON `class_s`.`id`=`stu`.`class_id` [0.063153s] # 这一行就是显示的原生sql语句用于调试

```

