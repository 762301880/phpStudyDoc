> 验证特俗格式 `59 23 L * *` 暂未通过测试

```php
<?php

function parseCronPart($part, $min, $max) {
    if ($part === '*') {
        return range($min, $max);
    }
    if (strpos($part, '/') !== false) {
        list($range, $step) = explode('/', $part);
        $step = (int) $step;
        if ($range === '*') {
            return range($min, $max, $step);
        }
    }
    if (strpos($part, '-') !== false) {
        list($start, $end) = explode('-', $part);
        return range((int) $start, (int) $end);
    }
    return [(int) $part];
}

function getNextCronTime($cronExpression) {
    $parts = explode(' ', $cronExpression);
    if (count($parts) !== 5) {
        throw new InvalidArgumentException("Invalid cron expression");
    }

    list($minutePart, $hourPart, $dayPart, $monthPart, $weekdayPart) = $parts;

    $minutes = parseCronPart($minutePart, 0, 59);
    $hours = parseCronPart($hourPart, 0, 23);
    $days = parseCronPart($dayPart, 1, 31);
    $months = parseCronPart($monthPart, 1, 12);
    $weekdays = parseCronPart($weekdayPart, 0, 6);

    $currentTime = new DateTime();
    $currentTime->setTime($currentTime->format('H'), $currentTime->format('i'), 0);

    while (true) {
        if (in_array((int) $currentTime->format('i'), $minutes) &&
            in_array((int) $currentTime->format('H'), $hours) &&
            in_array((int) $currentTime->format('d'), $days) &&
            in_array((int) $currentTime->format('m'), $months) &&
            in_array((int) $currentTime->format('w'), $weekdays)) {
            return $currentTime;
        }
        $currentTime->add(new DateInterval('PT1M'));
    }
}

// 测试
$cronExpression = '0 0 1,15 * *'; // 每周三午夜0点
$nextTime = getNextCronTime($cronExpression);
echo "Next cron time: " . $nextTime->format('Y-m-d H:i:s') . "\n";
```

