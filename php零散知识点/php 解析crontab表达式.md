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
        list($start, $end) = explode('-', $range);
        return range((int)$start, (int)$end, $step);
    }
    if (strpos($part, '-') !== false) {
        list($start, $end) = explode('-', $part);
        return range((int)$start, (int)$end);
    }
    if (strpos($part, ',') !== false) {
        return array_map('intval', explode(',', $part));
    }
    if ($part === 'L') {
        return ['L'];
    }
    if (strpos($part, 'W') !== false) {
        return [$part];
    }
    return [(int)$part];
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
        $currentDay = (int) $currentTime->format('d');
        $currentMonth = (int) $currentTime->format('m');
        $lastDayOfMonth = (int) $currentTime->format('t'); // Number of days in the current month

        // Handle 'W'
        $validDay = in_array($currentDay, $days);
        foreach ($days as $day) {
            if (is_string($day) && strpos($day, 'W') !== false) {
                $baseDay = (int)str_replace('W', '', $day);
                if ($baseDay > $lastDayOfMonth) continue;

                $closestWorkday = getClosestWorkday($currentTime, $baseDay);
                if ($currentDay == $closestWorkday) {
                    $validDay = true;
                    break;
                }
            }
        }

        if (in_array((int) $currentTime->format('i'), $minutes) &&
            in_array((int) $currentTime->format('H'), $hours) &&
            ($validDay || (in_array('L', $days) && $currentDay == $lastDayOfMonth)) &&
            in_array((int) $currentTime->format('m'), $months) &&
            in_array((int) $currentTime->format('w'), $weekdays)) {
            return $currentTime;
        }
        $currentTime->add(new DateInterval('PT1M'));
    }
}

function getClosestWorkday($date, $day) {
    $year = (int)$date->format('Y');
    $month = (int)$date->format('m');
    $targetDate = new DateTime("$year-$month-$day");

    $weekday = (int)$targetDate->format('N');
    if ($weekday <= 5) {
        return $day;
    } else {
        $previousWeekday = (clone $targetDate)->modify('last Friday')->format('d');
        $nextWeekday = (clone $targetDate)->modify('next Monday')->format('d');
        return (int)$previousWeekday < $day ? (int)$previousWeekday : (int)$nextWeekday;
    }
}

// 测试
$cronExpression = '0 8-17 * * *'; // 每月15日最近的工作日午夜0点
$nextTime = getNextCronTime($cronExpression);
echo "Next cron time: " . $nextTime->format('Y-m-d H:i:s') . "\n";

```

