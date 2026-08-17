## ps:不推荐好复杂 且业务代码啰嗦

### 注意

### PHP里直接用XA事务基本走不通

MySQL从5.7开始默认禁用`XA`，8.0虽支持但要求所有参与库都启用`xa_support=ON`且必须用`mysql`原生驱动（PDO不支持`XA START/END/COMMIT`命令），而PHP官方至今没在PDO或mysqli中暴露`XA`相关接口。你写`mysqli_query($conn, "XA START 'tx1'")`大概率报错`Unknown command`或被静默忽略。

## mysql_ XA事务文档

> [5.7](https://dev.mysql.com/doc/refman/5.7/en/xa-statements.html)

##  场景示例(日后补充)

### A库test_one表

```sql
CREATE TABLE `test_one` (
  `id` bigint unsigned NOT NULL AUTO_INCREMENT COMMENT '主键',
  `name` varchar(255) NOT NULL DEFAULT '' COMMENT '名称',
  `created_at` datetime DEFAULT NULL,
  `updated_at` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='A库测试表';
```

### B库test_two表

```sql
CREATE TABLE `test_two` (
  `id` bigint unsigned NOT NULL AUTO_INCREMENT COMMENT '主键',
  `title` varchar(255) NOT NULL DEFAULT '' COMMENT '标题',
  `created_at` datetime DEFAULT NULL,
  `updated_at` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='B库测试表';
```

### config配置**datebase.php**

```php

        'mysql' => [
            'driver' => 'mysql',
            'url' => env('DATABASE_URL'),
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => env('DB_DATABASE', 'forge'),
            'username' => env('DB_USERNAME', 'forge'),
            'password' => env('DB_PASSWORD', ''),
            'unix_socket' => env('DB_SOCKET', ''),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            'prefix' => '',
            'prefix_indexes' => true,
            'strict' => true,
            'engine' => null,
            'options' => extension_loaded('pdo_mysql') ? array_filter([
                PDO::MYSQL_ATTR_SSL_CA => env('MYSQL_ATTR_SSL_CA'),
            ]) : [],
        ],

        'mysql2' => [ // 第二个数据库连接 用于测试xa 分布式事务
            'driver' => 'mysql',
            'host' => env('DB2_HOST', '127.0.0.1'),
            'port' => env('DB2_PORT', '3306'),
            'database' => env('DB2_DATABASE', 'db2'),
            'username' => env('DB2_USERNAME'),
            'password' => env('DB2_PASSWORD'),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            'prefix' => '',
            'strict' => true,
            'engine' => null,
        ],
```





### 对应两个模型完整代码

#### app/Models/TestOne.php

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class TestOne extends Model
{
    protected $connection = 'mysql';
    protected $table = 'test_one';
    protected $fillable = ['name'];
}
```

#### app/Models/TestTwo.php

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class TestTwo extends Model
{
    protected $connection = 'mysql2';
    protected $table = 'test_two';
    protected $fillable = ['title'];
}
```

### 数据库连接配置 .env 示例

```php
# A库
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=db_a
DB_USERNAME=root
DB_PASSWORD=root

# B库
DB2_CONNECTION=mysql_second
DB2_HOST=127.0.0.1
DB2_PORT=3306
DB2_DATABASE=db_b
DB2_USERNAME=root
DB2_PASSWORD=root	
```

