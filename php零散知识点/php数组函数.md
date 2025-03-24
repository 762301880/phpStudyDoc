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

```php
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



```php
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

# 案例

## [删除数组中最后面几位](https://blog.csdn.net/weixin_42525582/article/details/115205188)

```php+HTML
<?php

$arr = [1 => '保洁服务', 2 => '保姆服务', 3 => '工程师服务'];
$unsetNum = 1; //需要删除的后几位位数
$newArr = array_splice($arr, 0, (count($arr) - $unsetNum));
var_dump($newArr);

# 返回

array(2) {
  [0]=>
  string(12) "保洁服务"
  [1]=>
  string(12) "保姆服务"
}
```

## 删除数组中的前几位

```php
<?php

$arr = [1 => '保洁服务', 2 => '保姆服务', 3 => '工程师服务'];
$unsetNum = 1; //需要删除的后几位位数
$newArr = array_slice($arr, 2);
var_dump($newArr);
```

#  二维数组进行排序

```php
<?php
$arr = [
    [
        "id" => 28,
        "validity_end_time" => "2023-02-04 15:50:00"
    ],
    [
        "id" => 33,
        "validity_end_time" => "2023-01-04 15:50:00"
    ],
    [
        "id" => 48,
        "validity_end_time" => "2023-03-04 15:50:00"
    ],
];
array_multisort( array_column($arr,'validity_end_time'), SORT_ASC,$arr);
var_dump($arr);

# 结果示例
array(3) {
  [0]=>
  array(2) {
    ["id"]=>
    int(33)
    ["validity_end_time"]=>
    string(19) "2023-01-04 15:50:00"
  }
  [1]=>
  array(2) {
    ["id"]=>
    int(28)
    ["validity_end_time"]=>
    string(19) "2023-02-04 15:50:00"
  }
  [2]=>
  array(2) {
    ["id"]=>
    int(48)
    ["validity_end_time"]=>
    string(19) "2023-03-04 15:50:00"
  }
}
```

# 二维数组比较交集

**参考资料**

| name     | url                                                          |
| -------- | ------------------------------------------------------------ |
| 参考博客 | [link](https://blog.csdn.net/weixin_43674113/article/details/106834127) |

## **个人解决示例(不推荐)**

```php
/**
 * 比较二位数组的交集
 * @param $array1
 * @param $array2
 * @return array
 */
function array_intersect_2d($array1,$array2){
    $intersectedData = array();

    foreach ($array1 as $item1) {
        foreach ($array2 as $item2) {
            if ($item1 === $item2) {
                $intersectedData[] = $item1;
                break;
            }
        }
    }
    return $intersectedData;
}

# 自定义一个二维数组
$array1 = array(
    array("id" => 1, "name" => "John"),
    array("id" => 2, "name" => "Jane"),
);

$array2 = array(
    array("id" => 2, "name" => "Jane"),
    array("id" => 3, "name" => "Bob"),
);

print_r(array_intersect_2d($array1,$array2));


## 结果返回示例
Array
(
    [0] => Array
        (
            [id] => 2
            [name] => Jane
        )

)
```

## chatgpt解决方案(推荐)

```php
// 两个示例二维数组
$array1 = array(
    array("id" => 1, "name" => "John"),
    array("id" => 2, "name" => "Jane"),
    array("id" => 3, "name" => "Bob")
);

$array2 = array(
    array("id" => 2, "name" => "Jane"),
    array("id" => 4, "name" => "Mary")
);

// 将二维数组展开为一维数组
$flattened_array1 = array_map('serialize', $array1); 
$flattened_array2 = array_map('serialize', $array2);

// 计算交集
$intersection = array_intersect($flattened_array1, $flattened_array2);

// 将结果转回二维数组
$result = array_map('unserialize', $intersection);

// 输出结果
print_r($result);


## 结果示例

Array
(
    [1] => Array
        (
            [id] => 2
            [name] => Jane
        )

)
```



# 二维数组比较差集

```php
function array_diff_2d($array1,$array2){
    // 将二维数组展开为一维数组
    $flattened_array1 = array_map('serialize', $array1);
    $flattened_array2 = array_map('serialize', $array2);

// 计算差集
    $difference = array_diff($flattened_array1, $flattened_array2);

// 将结果转回二维数组
    $result = array_map('unserialize', $difference);
    return $result;
}

// 两个示例二维数组
$array1 = array(
    array("id" => 1, "name" => "John"),
    array("id" => 2, "name" => "Jane"),
    array("id" => 3, "name" => "Bob")
);

$array2 = array(
    array("id" => 2, "name" => "Jane"),
    array("id" => 4, "name" => "Mary")
);



// 输出结果
print_r(array_diff_2d($array1,$array2));


## 结果示例

Array
(
    [0] => Array
        (
            [id] => 1
            [name] => John
        )

    [2] => Array
        (
            [id] => 3
            [name] => Bob
        )

)
```

# 依赖二维数组中的某一个值排序

## 参考资料

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://blog.csdn.net/lilongsy/article/details/104375413) |
|            |                                                              |

## 利用**array_multisort()**函数实现数组的二次排序

[**菜鸟教程-array_multisort()**](https://www.runoob.com/php/func-array-multisort.html)

**代码示例**

```shell
<?php

$arr=[['name'=>'张三','age'=>23],['name'=>'李四','age'=>28],['name'=>'小芳','age'=>18]];
$sortKeys=array_column($arr,'age'); //获取数组中的某一列
array_multisort($sortKeys, SORT_ASC, $arr);

print_r($arr);

# 返回示例
Array
(
    [0] => Array
        (
            [name] => 小芳
            [age] => 18
        )
    [1] => Array
        (
            [name] => 张三
            [age] => 23
        )
    [2] => Array
        (
            [name] => 李四
            [age] => 28
        )
)
```

# 如何获取数组第一个值和末尾值

> 使用current 获取 输出数组中的当前元素的值：每个数组中都有一个内部的指针指向它的"当前"元素，初始指向插入到数组中的第一个元素
>
> 使用end   将内部指针指向数组中的最后一个元素，并输出

```php
<?php
$times = ["2024-08-09", "2024-09-01"];

$start_time = current($times);
$end_time = end($times);
echo "数组第一个值是:{$start_time},数组末尾值是:{$end_time}";
```

# 判断传递过来的数据是不是json数据

```php
 /**
 * 判断是否是json格式
 */
if (!function_exists('isJson')) {

    function isJson($string)
    {
        json_decode($string);
        return (json_last_error() === JSON_ERROR_NONE);
    }
}

```

