# 一、上代码

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
```

