

**说明**

> call_user_func  可以让我们在不new一个方法的情况下实现回调一个函数，特定的场景很有用，例如
>
> 我们可以判断不同的条件回调不同的方法实现动态的实现不同的功能

**参考资料**

| 名称                       | 地址                                                         |
| -------------------------- | ------------------------------------------------------------ |
| call_user_func php官方手册 | [link](https://www.php.net/manual/zh/function.call-user-func.php) |
| 有空查看                   | [link](https://blog.csdn.net/weixin_33875839/article/details/94159380) |

**call_user_func  代码示例**

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;


class TestController extends Controller
{
    public function test(Request $request)
    {
        # 也可以这样调用 echo call_user_func([$this, 'retText'], 123, 456, 789); 
        echo call_user_func([TestController::class, 'retText'], 123, 456, 789); # 第一个参数是数组[需要调用的类位置,需要调用的方法名称],'多个参数','逗号分隔'
    }

    public function retText($text1, $text2, $text3)
    {
        return '您的返回字符是:' . $text1 . ' ' . $text2 . ' ' . $text3;
    }
}

# 调用test 控制器返回结果
您的返回字符是:123 456 789    
```

**call_user_func_array**

```php
# array方法是 call_user_func 的升级版 k
echo call_user_func([TestController::class, 'retText'],[ 123, 456, 789]);
```

