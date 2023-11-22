# 说明

> 假如有一张表例如下面这样 min代表最小的值，max代表等级的最大值,满足条件则返回对应的等级

| id   | min  | max  | title | created_at          | updated_at          |
| ---- | ---- | ---- | ----- | ------------------- | ------------------- |
| 1    | 0    | 52   | 潜水  | 2021-06-10 13:50:03 | 2021-06-10 13:50:03 |
| 2    | 53   | 100  | 冒泡  | 2021-06-10 13:50:03 | 2021-06-10 13:50:03 |
| 3    | 101  | 299  | 吐槽  | 2021-06-10 13:50:03 | 2021-06-10 13:50:03 |
| 4    | 300  | 500  | 活跃  | 2021-06-10 13:50:03 | 2021-06-10 13:50:03 |
| 5    | 501  | 0    | 传说  | 2021-06-10 13:50:03 | 2021-06-10 13:50:03 |



# 实现代码逻辑

> 核心思想
>
> 将对应的等级写入数组，循环判断是否满足条件如果满足返回对应的等级
>
> 因为4比较特殊没有最大值，所以需要在循环中单独判断

```php
  /**
     * 得到用户当前的成长值等级
     * @param integer $growth_point 成长值
     */
    public static function getClubGrowthTitle($growth_point)
    {
        $title = [0 => '潜水', 1 => '冒泡', 2 => '吐槽', 3 => '活跃', 4 => '传说'];
        foreach ($title as $key => $value) {
            if ($key == 4 && $growth_point >= ClubGrowthTitle::find(5)->getAttribute('min')) {
                return $title[4];
            }
            if ($growth_point >= ClubGrowthTitle::find($key + 1)->getAttribute('min') && $growth_point <= ClubGrowthTitle::find($key + 1)->getAttribute('max')) {
                return $title[$key];
            }
        }
    }             
```

