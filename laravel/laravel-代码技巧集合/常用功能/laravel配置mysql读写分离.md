## 说明 

> 本人是不建议在库中配置读写分离啊 现在都用中间件 例如 **ProxySQL**
>
> 具体更深研究请看我的文档 [中间件代理详细文档](../../../mysql/主从复制代理.md#三,ProxySQL(推荐-文档已完善))

## 资料

| 名称                      | 地址                                                         |
| ------------------------- | ------------------------------------------------------------ |
| laravel8文档-读写分离配置 | [link](https://learnku.com/docs/laravel/8.x/eloquent/9400#dd145f) |



## 读写分离配置

在 Laravel 中配置 MySQL 的读写分离数据库需要进行一些步骤。

读写分离是一种数据库优化策略，通过将读操作和写操作分别指向不同的数据库服务器，以减轻数据库服务器的负担并提高性能。

以下是配置 Laravel 中 MySQL 读写分离的一般步骤：

1. 打开 `.env` 文件
   在 Laravel 项目根目录下找到 `.env` 文件，这是配置环境变量的文件。打开该文件以进行配置。

2. 配置主数据库连接
   在 `.env` 文件中，找到以下配置项并填写主数据库的相关信息：

   ```ini
   DB_CONNECTION=mysql
   DB_HOST=主数据库主机名
   DB_PORT=3306
   DB_DATABASE=主数据库名称
   DB_USERNAME=用户名
   DB_PASSWORD=密码
   ```

3. 配置从数据库连接
   继续在 `.env` 文件中添加从数据库的配置项，通常可以添加多个从数据库配置，以实现负载均衡和故障容错。

   ```ini
   DB_CONNECTION_SECONDARY=mysql
   DB_HOST_SECONDARY=从数据库主机名
   DB_PORT_SECONDARY=3306
   DB_DATABASE_SECONDARY=从数据库名称
   DB_USERNAME_SECONDARY=用户名
   DB_PASSWORD_SECONDARY=密码
   ```

4. 配置数据库读写分离
   打开 `config/database.php` 文件，找到 `connections` 数组中的 `mysql` 配置项。在该配置项下，可以配置读写分离：

   ```php
   'mysql' => [
       'driver' => 'mysql',
       'read' => [
           'host' => env('DB_HOST', '主数据库主机名'),
       ],
       'write' => [
           'host' => env('DB_HOST_SECONDARY', '从数据库主机名'),
       ],
       'sticky' => true, // 可选，如果要保持同一请求内的读写一致性
       // ...
   ],
   ```

5. 使用读写分离
   Laravel 会自动根据配置的读写分离进行数据库操作。在代码中，你可以通过调用不同的方法来执行读操作和写操作，例如使用 `DB::select` 进行读操作，使用 Eloquent 模型进行写操作。

需要注意的是，读写分离是一个高级的数据库优化策略，需要综合考虑数据库的负载、网络延迟等因素。在配置时，也需要关注数据同步和一致性问题。在实际应用中，建议先进行测试和性能评估，确保读写分离能够为你的应用带来实际的性能提升。



## 使用读写分离

使用 Laravel 进行 MySQL 读写分离配置后，你可以在代码中直接进行数据库操作，Laravel 会根据配置自动将读操作和写操作分发到不同的数据库服务器。以下是一些在代码中如何使用的示例：

1. 进行读操作：

   ```php
   $results = DB::connection('mysql')->select('SELECT * FROM table_name');
   // 或者使用 Eloquent 查询
   $users = User::all();
   ```

2. 进行写操作：

   ```php
   DB::connection('mysql')->insert('INSERT INTO table_name (column1, column2) VALUES (?, ?)', [$value1, $value2]);
   // 或者使用 Eloquent 模型进行写操作
   $user = new User;
   $user->name = 'John';
   $user->email = 'john@example.com';
   $user->save();
   ```

需要注意的是，Laravel 会自动根据你的操作类型（读或写）选择合适的数据库连接。如果你想在一次请求中保持一致的读写连接，可以在 `config/database.php` 文件中设置 `'sticky' => true`，这样在同一个请求中，读写操作会使用同一连接。

在实际开发中，你可以根据需要选择在不同的情况下使用读写操作，例如在读多写少的情况下，可以将读操作分发到从数据库以减轻主数据库的负担。

总之，Laravel 的数据库操作非常灵活，读写分离的配置使得你可以更好地优化数据库性能和负载。确保在实际应用中进行充分的测试，以确保配置和性能达到预期效果。