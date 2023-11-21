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

**资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [菜鸟教程-sqlite安装](https://www.runoob.com/sqlite/sqlite-installation.html) |

### alpine安装扩展

```shell
apk add sqlite3  # 安装sqlite3扩展
apk add php-sqlite3 # 安装php扩展
```

### windows安装扩展

[**首先去sqlite官网下载sqlite3**](https://www.sqlite.org/download.html)

> - 您需要下载 **sqlite-tools-win32-\*.zip** 和 **sqlite-dll-win32-\*.zip** 压缩文件。

<img src="https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220802152316674.png" alt="image-20220802152316674" style="zoom: 67%;" />

**配置环境变量**

```shell
# 将下载的 sqlite-tools-win32-x86-3390200.zip  sqlite-dll-win32-x86-3390200.zip 解压到喜欢的目录
# 例如创建文件夹 D:\Program Files\sqlite3，并在此文件夹下解压上面两个压缩文件，将得到 sqlite3.def、sqlite3.dll 和 sqlite3.exe 文件。
```

配置环境变量

> 添加 `D:\Program Files\sqlite3`到 **PATH** 环境变量，最后在命令提示符下，使用 **sqlite3** 命令，将显示如下结果。

```shell
D:\>sqlite3
SQLite version 3.27.2 2019-02-25 16:06:06
Enter ".help" for usage hints.
Connected to a transient in-memory database.
Use ".open FILENAME" to reopen on a persistent database.
sqlite>
```

**配置php  sqlite扩展**

> 将**sqlite3.dll**文件复制到php扩展目录例如**D:\phpstudy_pro\Extensions\php\php7.4.3nts\ext**目录下

```shell
# 修改php.ini文件
extension=pdo_sqlite     # 找到这一行取消注释
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

> 这个错误通常是由于在使用PHP连接SQLite数据库时缺少SQLite驱动程序导致的。要解决这个问题，你需要安装并启用PHP中的SQLite驱动程序。你可以使用以下步骤来安装SQLite驱动程序：

```shell
bash-5.0# php -m

# 查询是否有次驱动
[PHP Modules]
....
sqlite3    # sqlite3驱动
....
[Zend Modules]
Zend OPcache


# -----------------------------------补充解决方案-------------------------------------------
## 打开终端（或命令提示符，如果在Windows上）并运行以下命令安装SQLite3和PHP SQLite扩展：

   sudo apt-get install sqlite3 libsqlite3-dev
   sudo apt-get install php7.x-sqlite3

## 注意：将“7.x”替换为你当前安装的PHP版本号，例如php7.4。

## 安装完成后，在PHP配置文件中启用SQLite扩展。在Ubuntu上，配置文件位于“/etc/php/7.x/apache2/php.ini” （将“7.x”替换为你当前安装的PHP版本号）。将以下行添加到配置文件底部：

   ```
   extension=sqlite3
   ```

## 重启Apache Web服务器，以便使更改生效：

   ```
   sudo systemctl restart apache2
   ```

#安装完成后，再次运行你的代码，这个问题应该就得以解决了。
```

**windows处理**

**配置php  sqlite扩展**

> 将**sqlite3.dll**文件复制到php扩展目录例如**D:\phpstudy_pro\Extensions\php\php7.4.3nts\ext**目录下
>
> ```shell
> 修改php.ini文件
> extension=pdo_sqlite     # 找到这一行取消注释
> ```







