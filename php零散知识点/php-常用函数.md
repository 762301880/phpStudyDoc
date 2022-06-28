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

**DateTime 操作时间**

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

