***author:***yaoliuyang

***created_at:***2021/08/13/14:49

# 资料&说明

## 说明

>好久没有接触thinkphp相关的项目了，这一期有一个关于后天的基于thinkphp6.0进行二开,
>
>对于一个laravel粉丝来说，think这个直接通过控制器名方法名当作api去访问的方式太令我感到
>
>恶心了，虽然think本身也是支持路由但是此二开项目原作者使用的不是路由的方式,所以只能跟着
>
>他的套路走

## 资料

| name                | url                                                        |
| ------------------- | ---------------------------------------------------------- |
| thinkphp6.0开发手册 | [link](https://www.kancloud.cn/manual/thinkphp6_0/content) |
|                     |                                                            |

# 使用示例

##  通过调用控制器的方式访问

- 创建控制器命令

  > 由官方给的控制器案例可以看出thinkphp不推荐创建的控制器名称像laravel的那种控制器定义，
  >
  > 例如 TestController   官方的推荐定义是 Test

```php
# 创建控制器 例:php think make:controller Test
php think make:controller  +你的控制器名称    
```

创建成功之后可以看见think为我们自动生成了crud 资源方法 通常我们不需要改变里面的方法直接使用即可

```php
<?php
declare (strict_types = 1);

namespace app\controller;

use think\Request;

class Test
{
    /**
     * 显示资源列表
     *
     * @return \think\Response
     */
    public function index()
    {
        //
        return 'Hello World';
    }
  /**
   * 省略下面的方法为了演示方便
   */
}

```

- 访问 index方法

  > 由此图可以看见只需要访问控制器名+方法名就可以充当我们的路由使用

<img src="https://i.loli.net/2021/08/13/tjpXcEIe5lTZxd9.png" alt="1628845646(1).jpg" style="zoom:50%;" />



## 通过路由的方式访问方法

- 在项目的route\app.php中定义

```php
/**
 * 第一个参数 test 就是我们需要访问的路由，
 * 第二个参数 /之前的test 指定了控制器名 /之后的index指定了方法名
 */
Route::get('test', 'test/index'); 
```

- api调用

```php
{{url}}/test # 效果和上图是一样的这里就不演示了
```

