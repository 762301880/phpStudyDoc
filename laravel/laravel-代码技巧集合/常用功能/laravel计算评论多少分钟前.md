# 说明

> 此记录用于计算你的评论时间传递函数即可

# 代码示例

- [第三方博客有空在研究](https://www.jianshu.com/p/b842f4dbea7a)

> 自己写的感觉可以如果不行日后补充，下次优化的时候打算采用数组取消if

```php
  public function getSerializeTime(Request $request)
    {
        $serializeTime = strtotime($request->get('time'));//传输过来的时间戳
        $serializeTime = time() - $serializeTime;//多少时间之前的时间戳
        $oneMinuteTimestamp = 60;//一分钟的时间戳
        $oneHourTimestamp = 60 * 60;//一小时的时间戳
        $oneDayTimestamp = 60 * 60 * 24;//一天的时间戳
        $oneMonthTimestamp = 60 * 60 * 24 * 30;//一个月的时间戳
        $oneYearTimestamp = 60 * 60 * 24 * 365;//一年的时间戳
        if ($serializeTime < $oneMinuteTimestamp) {
            return $serializeTime . '秒前';
        } elseif ($serializeTime < $oneHourTimestamp) {
            return floor($serializeTime / 60) . '分钟前';
        } elseif ($serializeTime < $oneDayTimestamp) {
            return floor($serializeTime / (60 * 60)) . '小时前';
        } elseif ($serializeTime < $oneMonthTimestamp) {
            return floor($serializeTime / (60 * 60 * 24)) . '天前';
        } elseif ($serializeTime < $oneYearTimestamp) {
            return floor($serializeTime / (60 * 60 * 24 * 30)) . '月前';
        } else {
            # 如果大于一年直接返回时间
            return date('Y-m-d H:i:s', strtotime($request->get('time')));
        }
    }

# --------------------------------我是分割-----------------------------------------------------------------

# 优化封装二 (全局函数)
if (!function_exists('getBeforeTime')) {
    /**
     * 计算序列化时间
     * 调用示例 dd(getBeforeTime(strtotime('1997-08-11 09:10:12')));
     */
    function getBeforeTime($time)
    {
        //闭包序列化时间 int格式
        $serializeTime = function ($time) {
            if (is_int($time)) $time = (int)$time;
            if (is_string($time)) $time = strtotime($time);
            return $time;
        };
        $thisTime = (new DateTime())->format('Y-m-d H:i:s');
        $thisTime = (new DateTime($thisTime))->getTimestamp();
        if ($thisTime < $time) throw new Exception("需要转化的时间不能大于当前的日期");
        $time = $serializeTime($time);
        $seconds = 60;//60秒
        $minutes = $seconds * $seconds;//1小时
        $hours = $minutes * 24;//一天
        $month = $hours * 30;//一个月
        $year = $month * 12;//一年
        $max_year = $year * 100;//最大年份100年
        //echo "当前时间为:" . $thisTime;
        //echo "需要转化的时间为:" . $time;
        $timeDiff = $thisTime - $time;
        //echo "时间差为:" . $timeDiff;
        if ($timeDiff < $seconds) return $timeDiff . '秒之前';
        if ($timeDiff < $minutes) return floor($timeDiff / $seconds) . '分钟之前';
        if ($timeDiff < $hours) return floor($timeDiff / $minutes) . '小时之前';
        if ($timeDiff < $month) return floor($timeDiff / $hours) . '天之前';
        if ($timeDiff < $year) return floor($timeDiff / $month) . '个月之前';
        if ($timeDiff > $year && $timeDiff < $max_year) return floor($timeDiff / $year) . "年之前";
        return date('Y-m-d H:i:s', $time); //超过100年返回具体时间
    }
}
```

**实例二**

<img src="https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/58eNdiQ1D9OZMmc.png" alt="1648196860(1).jpg" style="zoom:50%;" />

```php
/**
     * 序列化时间
     * @param $time
     * @return false|string|void
     */
    protected function getSerializeTime($time)
    {
        $serializeTime = strtotime($time);//传输过来的时间戳
        $afterSerializeTime = (strtotime((date('Y-m-d', $serializeTime) . ' 23:59:59'))); # 当前时间距离24点之前的时间戳
        $beforeSerializeTime = time() - $serializeTime;//多少时间之前的时间戳
        $oneMinuteTimestamp = 60;//一分钟的时间戳
        $oneHourTimestamp = 60 * 60;//一小时的时间戳
        $oneDayTimestamp = 60 * 60 * 24;//一天的时间戳
//        $oneMonthTimestamp = 60 * 60 * 24 * 30;//一个月的时间戳
//        $oneYearTimestamp = 60 * 60 * 24 * 365;//一年的时间戳
        if ($beforeSerializeTime < $oneMinuteTimestamp) { # 发布时间一分钟内，显示：刚刚
            return "刚刚";
        } elseif ($beforeSerializeTime < $oneHourTimestamp) { # 发布时间一小时内，显示：XX分钟前（45分钟前）
            return floor($beforeSerializeTime / 60) . '分钟前';
        } elseif (($beforeSerializeTime < $oneDayTimestamp) && ($serializeTime + ($afterSerializeTime - $serializeTime) > time())) { # 发布时间超过一小时,显示：XX小时前（12小时前）
            return floor($beforeSerializeTime / (60 * 60)) . '小时前';
        } elseif (((($serializeTime + ($afterSerializeTime - $serializeTime))) + $oneDayTimestamp) > time()) { # 超过当天23:59:59，显示：昨天 XX:XX（昨天 15:20)
            return "昨天" . " " . date('H:i', $serializeTime);
        } elseif (((($serializeTime + ($afterSerializeTime - $serializeTime))) + $oneDayTimestamp) < time()) {
            return date('Y-m-d', $serializeTime);
        }
    }
```

