##  官方文档

> https://doc.thinkphp.cn/v5_1/URLfangwen.html

## nginx转发规则

> 在Nginx低版本中，是不支持PATHINFO的，但是可以通过在`Nginx.conf`中配置转发规则实现
>
> 这段代码是 Nginx 配置文件中的一个 location 块配置，主要用于 URL 重写（rewrite），
>
> 通常在使用 PHP 框架（如 ThinkPHP、Laravel 等）时会用到，目的是实现 “伪静态” 或 “路径重写” 功能。


具体解释如下：

> 1. `location / { ... }`：表示匹配网站根目录下的所有请求。
> 2. `if (!-e $request_filename) { ... }`：这是一个条件判断，意思是 “如果请求的文件或目录不存在”：
>    - `$request_filename` 是 Nginx 的内置变量，表示当前请求对应的服务器端文件路径
>    - `-e` 是 Nginx 的文件检测运算符，用于判断文件或目录是否存在
>    - `!` 表示否定，所以整个条件的意思是 “当请求的文件 / 目录不存在时”
> 3. `rewrite ^(.*)$ /index.php?s=/$1 last;`：这是重写规则，意思是：
>    - `^(.*)$`：正则表达式，匹配整个请求的 URI（例如请求 `/user/123` 会被匹配）
>    - `/index.php?s=/$1`：重写后的目标 URL，`$1` 表示前面正则匹配到的内容（例如上面的例子会被重写成 `/index.php?s=/user/123`）
>    - `last`：重写标记，表示完成当前重写后，不再执行后续的重写规则，而是用新的 URL 重新匹配 location 块
>
> **整体作用**：
> 当用户访问一个不存在的文件或目录时（通常是框架的路由地址，如 `/article/1`），Nginx 会把请求重写到 `index.php` 并带上原始路径参数（`s=/article/1`），由 PHP 框架的入口文件（index.php）统一处理这个请求，实现基于 URL 的路由功能。
>
> 这是 PHP 框架常用的 Nginx 配置方式，目的是隐藏入口文件 index.php，让 URL 更简洁美观（如 `/user/123` 替代 `/index.php?c=user&a=detail&id=123`）。

```nginx
location / { // …..省略部分代码
   if (!-e $request_filename) {
   		rewrite  ^(.*)$  /index.php?s=/$1  last;
    }
}
```

