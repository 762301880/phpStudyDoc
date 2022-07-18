# 代码示例

## 算法1

```php
$data = ['2017-08-24',
        '2017-08-26',
        '2017-08-28',
        '2017-08-30',
        '2017-08-31'];
$len = count($data);
$oneDaySecond = 86400;
$newData = array();
for ($i = 1; $i < $len; $i++) {
    $previous = strtotime(str_replace('.', '-', $data[($i - 1)]));
    $current = strtotime(str_replace('.', '-', $data[($i)]));
    $diff = $current - $previous;
    if (!in_array($data[($i - 1)], $newData, true)) {
        $newData[] = $data[($i - 1)];
    }
    if ($diff > $oneDaySecond) {
        for ($m = $previous + $oneDaySecond; $m < $current; $m += $oneDaySecond) {
            $newData[] = date('Y-m-d', $m);
        }
    }
    $newData[] = $data[$i];
}
print_r($newData);
```

## 算法二

```php
# 更简便的算法
#注意事项 日期必须使用'' 字符类型 因为日期不是数值类型
       $collection = [];
        //循环计算
        $minimum_time = strtotime($minimum_time);
        $maximum_time = strtotime($maximum_time);
        while ($minimum_time <= $maximum_time) {
            $collection[] += $minimum_time;
            $minimum_time = strtotime('+1 day', $minimum_time);
        }
        foreach ($collection as &$value) {
            $value = date('Y-m-d', $value);
        }
        return $collection;
```

# [计算时间是上午还是下午](https://blog.csdn.net/weixin_39771260/article/details/115148038)



**示例**

```php
$hours=date('H');# 自动获取当前的小时
if($hours >= 7 && $Datetime =< 12) return "上午";
if($hours >= 12 && $Datetime =< 18) return "x";
```

