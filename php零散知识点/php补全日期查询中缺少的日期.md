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

# **PHP计算两个日期相差几个月多余几天**

**参考资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://download.csdn.net/download/lingyun820/34652556?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-download-2%7Edefault%7ECTRLIST%7EPaid-1-34652556-blog-116123766.pc_relevant_multi_platform_whitelistv3&depth_1-utm_source=distribute.pc_relevant.none-task-download-2%7Edefault%7ECTRLIST%7EPaid-1-34652556-blog-116123766.pc_relevant_multi_platform_whitelistv3&utm_relevant_index=2) |



**参考的代码示例**

```shell
<?php
//计算两个日期相差几个月多余几天
function getMonthAndDay($date1, $date2)
{
    $datestart = date('Y-m-d', strtotime($date1)); //讲日期转化为年月日格式
    if (strtotime($datestart) > strtotime($date2)) { //判断第一日期是否大于第二日期 如果成立转换位置
        $tmp = $date2;
        $date2 = $datestart;
        $datestart = $tmp;
    }
    list($Y1, $m1, $d1) = explode('-', $datestart); //日期一年月日
    list($Y2, $m2, $d2) = explode('-', $date2);//日期二年月日
    $Y = $Y2 - $Y1;
    $m = $m2 - $m1;
    $d = $d2 - $d1;
    if ($d < 0) {
        $d += (int)date('t', strtotime("-1 month $date2"));
        $m--;
    }
    if ($m < 0) {
        $m += 12;
        $Y--;
    }
    $res['month'] = 0;
    if ($Y == 0) {
        $res['month'] = $m;
        $res['day'] = $d;
        return $res;
    } elseif ($Y == 0 && $m == 0) {
        $res['day'] = $d;
        return $res;
    } else {
        $res['month'] = $m + $Y * 12;
        $res['day'] = $d;
        return $res;
    }
}

print_r(getMonthAndDay('2022-05-26', '2022-07-27'));
```

