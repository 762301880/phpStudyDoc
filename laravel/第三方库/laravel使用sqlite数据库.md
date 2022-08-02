#  项目配置

## navicat配置或创建数据库

> 点击左上角的**连接->SQLite**，可以选择**现有的数据库文件(直接指向已经存在的数据库)**或者新建一个数据库都行
>
> **创建出来的数据库默认以 .db结尾例如(demo.db)**其中默认有一个**main**数据库是我们的主数据库写代码的时候不需要指定数据库
>
> 名称

<img src="https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220802145419494.png" alt="image-20220802145419494" style="zoom:50%;" />

## **laravel项目配置**

> 在**you_project\config\database.php**中的**connections**中配置
>
> 我们只需要修改**database**路径即可，因为sqlite不需要用户名密码连接直接可以使用的轻量级数据库，
>
> 也可以说是一个文件，文件就是数据库

```php

# windows 配置
       'sqlite' => [
            'driver' => 'sqlite',
            'url' => env('DATABASE_URL'),
            'database' => 'D:\Program Files\sqlite3\demo.db',
            'prefix' => '',
            'foreign_key_constraints' => env('DB_FOREIGN_KEYS', true),
        ],

# linux配置
       'sqlite' => [
            'driver' => 'sqlite',
            'url' => env('DATABASE_URL'),
            //'database' =>'/data/work/laravel_study/database/demo.db',       # 绝对路径
            //'database' =>'../database/demo.db',         
            'database' =>__DIR__.'/../database/demo.db',
            'prefix' => '',
            'foreign_key_constraints' => env('DB_FOREIGN_KEYS', true),
        ],
```

## 安装sqlite3扩展

### alpine安装扩展

```shell
apk add sqlite3  # 安装sqlite3扩展
apk add php-sqlite3 # 安装php扩展
```



## 使用示例

### 指定连接通道查询(不推荐)

```php
        # 指定连接操作
        $bool=DB::connection('sqlite')->table('user')->insert(['name'=>'zhangsan']); # 插入数据库信息
        var_dump($bool); 
        $user=DB::connection('sqlite')->table('user')->get(); # 查询数据库信息
        var_dump($user); 
```

### 配置连接(推荐)

> 上一步我们可以发现每次操作**sqlite数据库之前还需要指定连接 connection("连接名")**很是不方便这时候我们就可以在**.env里面修改数据库的默认连接**

​	**修改.env**

```shell
DB_CONNECTION=sqlite     # 指定DB连接为sqlite
```



# 注意事项

## 报错:General error: 8 attempt to write a readonly database

**参考资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/u010496966/article/details/91043201) |

> 一般错误:8尝试写入只读数据库,执行下面的**sqlite插入操作我以为是数据库只读**，结果最后发现是**服务器database目录没有权限**

**执行的sql语句**

```php
$user=DB::connection('sqlite')->table('user')->insert(['name'=>'张三']);
```

**解决方案**

```shell
# 解决方案给项目目录的 databases文件夹设置一个最大权限
  chmod -R 755 storage/
```



##  报错:Illuminate\Database\QueryException: could not find driver

> 此错误说明找不到**php扩展**

```shell
bash-5.0# php -m

# 查询是否有次驱动
[PHP Modules]
....
sqlite3    # sqlite3驱动
....
[Zend Modules]
Zend OPcache
```