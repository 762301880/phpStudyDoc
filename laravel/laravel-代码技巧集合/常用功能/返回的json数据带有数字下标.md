# 如下所示

> 如图所示的数据中，不知道我们是什么时候 会操作数组或者其他操作就会生成下标
>
> 参考资料 ：https://www.php.cn/wenda/1913.html

```shell
<?php

$arr = array(
    '1' => array('name'=>'test1'),
    '2' => array('name'=>'test1'),
    '3' => array('name'=>'test1'),
    '4' => array('name'=>'test1'),
    '5' => array('name'=>'test1'),
);
#$arr = array_values($arr);
echo json_encode($arr);
```

**打印结果**

```shell
{"1":{"name":"test1"},"2":{"name":"test1"},"3":{"name":"test1"},"4":{"name":"test1"},"5":{"name":"test1"}}
```

## 修改方案

> 值获取数组中值的key即可

```shell
<?php

$arr = array(
    '1' => array('name'=>'test1'),
    '2' => array('name'=>'test1'),
    '3' => array('name'=>'test1'),
    '4' => array('name'=>'test1'),
    '5' => array('name'=>'test1'),
);
$arr = array_values($arr);
echo json_encode($arr);
```

**打印结果**

```php
[{"name":"test1"},{"name":"test1"},{"name":"test1"},{"name":"test1"},{"name":"test1"}]
```

