# 代码示例

##  代码生成日期(不推荐没有索引)

### 算法1

```php
$data = [
        '2017-08-24',
        '2017-08-26',
        '2017-08-28',
        '2017-08-30',
        '2017-08-31'
];
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

### 算法二

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

## 批量插入数据库生成日期

### python脚本  按年插入日期(推荐)

```python
import pymysql
from datetime import datetime, timedelta

# ====================== 你的数据库配置（直接填你的）======================
db_config = {
    'host': 'mysql7.sqlpub.com',
    'user': 'laravel_test',
    'password': '****************',  # 你的密码
    'database': 'laravel_test',
    'charset': 'utf8mb4',
    'port': 3312,  # 你用的是 3312 端口
}

# 生成日期范围（2020-2030 足够用）
START_DATE = "1997-01-01"
END_DATE = "2040-12-31"

# ====================== 连接数据库 ======================
try:
    conn = pymysql.connect(**db_config)
    cursor = conn.cursor()
    print("✅ 数据库连接成功")
except Exception as e:
    print("❌ 数据库连接失败：", e)
    exit()

# ====================== 生成连续日期 ======================
start = datetime.strptime(START_DATE, "%Y-%m-%d")
end = datetime.strptime(END_DATE, "%Y-%m-%d")
dates = []

current = start
while current <= end:
    dates.append((current.strftime("%Y-%m-%d"),))
    current += timedelta(days=1)

# ====================== 批量插入 ======================
sql = "INSERT IGNORE INTO days (date) VALUES (%s)"

try:
    cursor.executemany(sql, dates)
    conn.commit()
    print(f"✅ 插入完成！共 {len(dates)} 条日期数据")
except Exception as e:
    print("❌ 插入失败：", e)
    conn.rollback()

# 关闭
cursor.close()
conn.close()
```



#  补充

## [计算时间是上午还是下午](https://blog.csdn.net/weixin_39771260/article/details/115148038)



**示例**

```php
$hours=date('H');# 自动获取当前的小时
if($hours >= 7 && $Datetime =< 12) return "上午";
if($hours >= 12 && $Datetime =< 18) return "下午";
```

## **PHP计算两个日期相差几个月多余几天**

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

