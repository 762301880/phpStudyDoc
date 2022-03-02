# 参考资料

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://blog.csdn.net/lilongsy/article/details/104375413) |
|            |                                                              |



# 利用**array_multisort()**函数实现数组的二次排序

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

