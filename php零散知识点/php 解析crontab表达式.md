> 验证特俗格式 `59 23 L * *` 暂未通过测试

```php
<?php

function parseCronPart($part, $min, $max) {
    // 解析 cron 表达式的一部分
    if ($part === '*') {
        return range($min, $max); // 如果是 '*'，则返回范围从 $min 到 $max 的数组
    }
    if (strpos($part, '/') !== false) {
        // 如果包含 '/'，则表示间隔执行
        list($range, $step) = explode('/', $part);
        $step = (int) $step;
        if ($range === '*') {
            return range($min, $max, $step); // 如果范围是 '*'，则返回范围从 $min 到 $max，以 $step 为间隔的数组
        }
        list($start, $end) = explode('-', $range);
        return range((int)$start, (int)$end, $step); // 返回范围从 $start 到 $end，以 $step 为间隔的数组
    }
    if (strpos($part, '-') !== false) {
        // 如果包含 '-'，则表示范围
        list($start, $end) = explode('-', $part);
        return range((int)$start, (int)$end); // 返回范围从 $start 到 $end 的数组
    }
    if (strpos($part, ',') !== false) {
        // 如果包含 ','，则表示多个值
        return array_map('intval', explode(',', $part)); // 返回由逗号分隔的多个值组成的数组
    }
    if ($part === 'L') {
        return ['L']; // 如果是 'L'，则返回包含单个 'L' 的数组
    }
    if (strpos($part, 'W') !== false) {
        return [$part]; // 如果包含 'W'，则返回包含该值的数组
    }
    return [(int)$part]; // 否则，将其解析为整数，并返回一个包含该整数的数组
}

function getNextCronTime($cronExpression) {
    $parts = explode(' ', $cronExpression);
    if (count($parts) !== 5) {
        throw new InvalidArgumentException("Invalid cron expression");
    }

    list($minutePart, $hourPart, $dayPart, $monthPart, $weekdayPart) = $parts;

    $minutes = parseCronPart($minutePart, 0, 59); // 解析分钟部分
    $hours = parseCronPart($hourPart, 0, 23); // 解析小时部分
    $days = parseCronPart($dayPart, 1, 31); // 解析日期部分
    $months = parseCronPart($monthPart, 1, 12); // 解析月份部分
    $weekdays = parseCronPart($weekdayPart, 0, 6); // 解析星期部分

    // 计算每天最近的工作日
    $lastDayOfMonth = (int) date('t'); // 当前月份的最后一天
    $closestWorkdays = [];
    for ($day = 1; $day <= $lastDayOfMonth; $day++) {
        $closestWorkdays[$day] = getClosestWorkday($day); // 获取每天的最近工作日，并保存到数组中
    }

    $currentTime = new DateTime(); // 当前时间
    $currentTime->setTime($currentTime->format('H'), $currentTime->format('i'), 0); // 设置时间的时、分、秒为当前时间

    while (true) {
        $currentDay = (int) $currentTime->format('d'); // 当前日期
        $currentDayW = $closestWorkdays[$currentDay]; // 获取当前日期的最近工作日

        // 检查当前时间是否符合 cron 表达式的条件
        $validDay = in_array($currentDay, $days) || (in_array('L', $days) && $currentDay == $lastDayOfMonth) ||
            (in_array("$currentDayW", $days) && $currentDayW == $closestWorkdays[$currentDay]);

        if (in_array((int) $currentTime->format('i'), $minutes) && // 分钟匹配
            in_array((int) $currentTime->format('H'), $hours) && // 小时匹配
            $validDay && // 日期匹配
            in_array((int) $currentTime->format('m'), $months) && // 月份匹配
            in_array((int) $currentTime->format('w'), $weekdays)) { // 星期匹配
            return $currentTime; // 返回符合条件的时间
        }
        $currentTime->add(new DateInterval('PT1M')); // 增加一分钟，准备下一次检查
    }
}

function getClosestWorkday($day) {
    // 计算指定日期的最近工作日
    $currentMonth = (int) date('m'); // 当前月份
    $currentYear = (int) date('Y'); // 当前年份

    $targetDate = new DateTime("$currentYear-$currentMonth-$day"); // 创建指定日期的 DateTime 对象

    $weekday = (int) $targetDate->format('N'); // 获取指定日期的星期几
    if ($weekday <= 5) {
        return $day; // 如果是工作日（星期一到星期五），直接返回该日期
    } else {
        // 如果是非工作日，找到最近的工作日
        $previousWeekday = (clone $targetDate)->modify('last Friday')->format('d'); // 上一个星期五
        $nextWeekday = (clone $targetDate)->modify('next Monday')->format('d'); // 下一个星期一
        return (int) $previousWeekday < $day ? (int) $previousWeekday : (int) $nextWeekday; // 返回最近的工作日
    }
}

// 测试
$cronExpression = '0 12 1W * *'; // 每月15日最近的工作日午夜0点
$nextTime = getNextCronTime($cronExpression);
echo "Next cron time: " . $nextTime->format('Y-m-d H:i:s') . "\n";

```

