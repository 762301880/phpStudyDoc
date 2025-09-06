## [php函数大全](https://www.p2hp.com/phpfuncs.html)



## abs 返回不同数的绝对值:(负数取整)

>  “ABS”是“Absolute”的缩写，意思是“绝对的”

**资料**

| 名称                              | 地址                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| 菜鸟教程-abs() 函数     php中文网 | [link](https://www.runoob.com/php/func-math-abs.html)  [link](https://www.php.cn/php-ask-487279.html) |

**示例**

```php
<?php

echo abs(-1).PHP_EOL;
echo abs(-7.5).PHP_EOL;
echo abs(9.9).PHP_EOL;
echo abs(8).PHP_EOL;

# 输出
1
7.5
9.9
8
```

[**php控制台输入输出**](https://blog.csdn.net/weixin_34377065/article/details/92143298?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-1-92143298-null-null.pc_agg_new_rank&utm_term=php+%E6%8E%A7%E5%88%B6%E5%8F%B0%E8%BE%93%E5%85%A5&spm=1000.2123.3001.4430)

```php
# echo.php
<?php
fwrite(STDOUT, "请输入消息："); 
$msg = trim(fgets(STDIN)); 
echo $msg;

# 终端使用
$ php echo.php
请输入消息：123
123
```

## **DateTime 操作时间**

**说明**

> 我们可以利用**DateTime **类计算两个日期的差集

**参考资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://blog.csdn.net/u014158869/article/details/87885478) |

**代码示例**

```php
  /**
     * 获取已服务时长
     * 超过一个月以上刚开始四天之内算一个月
     */
    protected function getServiceTime(OrderModel $orderModel)
    {
        $to_door_time = !empty($orderModel->to_door_time) ? $orderModel->to_door_time : "";//上户日期
        //$to_door_time = date('Y-m-d H:i:s', strtotime('+1 month')); //测试写死到店时间
        $currentDateTime = date('Y-m-d H:i:s');
        $to_door_time = new \DateTime($to_door_time);
        $currentDateTime = new \DateTime($currentDateTime);
        $m = $to_door_time->diff($currentDateTime)->m;//超过的月份
        $currentDay = date('d');//当前的天数
        $addMonth = $currentDay > self::MORE_THAN_DAY ? 1 : 0;
        return $m + $addMonth;
    }
```

## clone 

> 当我们需要创建一个对象的副本，并且希望这个副本是独立的、不受原始对象的影响时，可以使用 PHP 的 `clone` 函数。下面详细解释一下 `clone` 函数的相关知识点：
>
> 1. `clone` 的语法：`$newObject = clone $oldObject;`。`clone` 函数接受一个对象作为参数，并返回该对象的一个副本。
> 2. 浅拷贝和深拷贝：默认情况下，`clone` 函数进行的是浅拷贝。浅拷贝会复制对象的属性和方法，但不会复制引用类型的属性。也就是说，新的对象和原始对象会共享一些属性的引用。如果需要进行深拷贝，可以在对象的 `__clone` 方法中手动处理引用类型的属性。
> 3. `__clone` 方法：如果一个类定义了 `__clone` 方法，那么在调用 `clone` 函数时，会自动调用该方法。我们可以在 `__clone` 方法中实现自定义的深拷贝逻辑，以确保复制对象的所有属性和引用类型的属性。
> 4. 对象的克隆和引用：克隆一个对象后，新对象和原对象是两个独立的实例，对其中一个对象的修改不会影响到另一个对象。但是，如果对象中包含了引用类型的属性，那么新对象和原对象仍然会共享这些属性的引用。如果希望新对象也拥有独立的引用类型属性，需要在 `__clone` 方法中对这些属性进行深拷贝。
> 5. 克隆的适用场景：克隆通常用于创建对象的备份、缓存或者创建新对象时的初始状态。通过克隆对象，可以避免重复创建相同的对象，提高性能和减少资源消耗。

**应用场景示例**

```php
$query=Db::table('think_user');
$query=$query->order('create_time')
->where('status',1);
$count=$query->count();
$list=$query->select();

# 以上写法是错误的
# 你在执行 $count 语句之后，再次执行 $list 语句时，实际上是在同一个查询对象上进行操作。这意味着 $list 的查询条件也会包括 $count 的条件，因此可能会得到错误的结果。
# 为了避免这个问题，你可以使用 clone 关键字来创建一个新的查询对象，然后在新对象上执行 $list 的查询。这样就能确保 $count 和 $list 的查询条件是独立的。

# 下面是修正后的代码示例：

$query = Db::table('think_user');
$query = $query->order('create_time')->where('status', 1);
$countQuery = clone $query; // 克隆一个新的查询对象
$count = $countQuery->count(); //或者简写为 (clone $query)->count();
$list = $query->select();

# 通过使用 clone 关键字，我们创建了一个新的查询对象 $countQuery，它和 $query 对象具有相同的查询条件。然后，我们分别在 $countQuery 和 $query 上执行了 $count 和 $list 的查询。这样就可以在 $count 查询中携带上一个 $query 对象的查询条件了。
```

## 三元运算符



资料

| 名称       | 地址                                                  |
| ---------- | ----------------------------------------------------- |
| PHP 运算符 | [链接](https://www.runoob.com/php/php-operators.html) |
|            |                                                       |

说明

> 三元运算不是什么高大上的东西 就是判断第一个条件是否为真  如果为真返回第一个条件反之
>
> 返回第二个条件，这里我为什么补充一下这个三元运算可以减少很多 if else 操作

代码示例

```php
$a = 2;
$temp=($a > 1 && $a == 2) ? 'yes' : 'no';
return $temp;
```

**多重三目运算符**

**参考资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://blog.csdn.net/qq_35641337/article/details/89154017) |



> 如下所示我们希望如果 **$a1** **$a2**变量如果都存在的话输出 **$a1*$a2**,如果**$a1**第一个值如果不为空的情况下直接返回**$a1**
>
> 反之的话直接返回空字符          
>
> **执行顺序**
>
> 这里执行如果第一个结果为**true**的时候直接执行**:**之前的逻辑，反之会进入**:**之后的逻辑 会先执行括号中的第二次三目运算返回 **$a1**变量
>
> 还是空字符

```shell
        $a1 = '';
        $a2 = '';
        $test = (!empty($a1) & !empty($a2)) ? $a1 . '*' . $a2 : (!empty($a1) ? $a1 : "空");
        echo $test;
```

多重再多重三目运算符

> 如下所示我们希望如果 **$a1** **$a2**变量如果都存在的话输出 **$a1*$a2**,如果**$a1**第一个值如果不为空的情况下直接返回**$a1**反之的话直接返回空字符 ,
>
> 如果**$a1**不存在
>
> **$a2**存在的话输出`*$a2`
>
> 反之的话直接返回空字符         

```shell
        $a1 = '';
        $a2 = '456';

        $test = (!empty($a1) & !empty($a2)) ? $a1 . '*' . $a2 : ((empty($a1) & !empty($a2) ? '*' . $a2 : (!empty($a1) ? $a1 : "空")));
        echo $test;
```

**这里推荐写回调**

> 三目虽然好用但是长了自己看着就很头疼还不如回调看着结构清晰

```shell
    /**
     * 生成返回pxs_customer_bing_spec标特殊字段返回
     * @param $text1
     * @param $text2
     * @return string
     */
    protected function generateReturnCustomerBingSpecText($text1, $text2)
    {
        if (!empty($text1) && !empty($text2)) {
            return $text1 . '*' . $text2;
        }
        if (empty($text1) && !empty($text2)) {
            return '*' . $text2;
        }
        if (!empty($text1)) {
            return $text1;
        }
        return '';
    }
```

