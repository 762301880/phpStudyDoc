# 参考资料

| 名称            | 地址                                                         |
| --------------- | ------------------------------------------------------------ |
| php_callble官网 | [link](https://www.php.net/manual/zh/language.types.callable.php) |
| 第三方博客      | [link](https://learnku.com/articles/57330)                   |



# 代码示例

```php
class TestController extends Controller
{
    public function test(Request $request)
    {
//        return $this->getUserInfo('张三', '男', function ($name, $sex) {
//            var_dump($name, $sex);
//        });
        return $this->getUserInfo('张三', '男', [$this, 'cccc']);
    }

    public function getUserInfo($name, $sex, callable $callback)
    {
        $info = '姓名:' . $name . ',' . '性别:' . $sex . PHP_EOL;
        call_user_func($callback, $name, $sex);
        return $info;
    }

    public function cccc(...$params)
    {
        var_dump($params);
    }
}
```

**显示结果**

```php
array(2) { [0]=> string(6) "张三" [1]=> string(3) "男" }  姓名:张三,性别:男
```

