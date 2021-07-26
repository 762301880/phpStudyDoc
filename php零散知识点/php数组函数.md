# 资料

| name            | url                                                 |
| --------------- | --------------------------------------------------- |
| php官网数组详解 | [link](https://www.php.net/manual/zh/ref.array.php) |
|                 |                                                     |



# 数组函数使用示例

## [array_filter](https://www.php.net/manual/zh/function.array-filter.php)

> 参数
>
> 参数1 需要过滤的数组 
>
> 参数2 闭包函数callback

```shell
 # 用法一过滤数组中的空值
 $arr=[0=>'张三',1=>'李四',2=>'',3=>'拉拉'];
 dd(array_filter($arr));
 # 输出
 array:3 [
  0 => "张三"
  1 => "李四"
  3 => "拉拉"
]
/***************************************分隔符*****************************************************/
# 更多过滤条件
$arr = [
            0 => 'foo',
            1 => -1,
            2 => false, # boolean 假会被过滤
            3 => null,  # 空值会被过滤
            4 => '',    # 空字符会被过滤
            5 => '0',   # 字符0 也会被 过滤
            6 => 0,     # 0 会被过滤
        ];
        dd(array_filter($arr));
# 输出
array:2 [
  0 => "foo"
  1 => -1
]
```

## [array_sum](https://www.php.net/manual/zh/function.array-sum.php)



```shell
        $arr = [
            0 => 1,
            1 => -1,    # 负数会被累加
            2 => 2,
            3 => '5',   #  字符数值类型会被累加
            4 => '您好' #  字符默认会被过滤
        ];
       dd(array_sum($arr));
       # 结果 7
```

