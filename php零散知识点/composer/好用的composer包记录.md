## 文字转拼音

### [overtrue/pinyin](http://packagist.p2hp.com/packages/overtrue/pinyin#4.x-dev)

## 中国农历转换与查询工具

### [overtrue/chinese-calendar](https://packagist.org/packages/overtrue/chinese-calendar)

## IP地址

**资料**

| 名称                                                         | 地址                                                       |
| ------------------------------------------------------------ | ---------------------------------------------------------- |
| [zoujingli/](https://packagist.org/packages/zoujingli/)ip2region | [link](https://packagist.org/packages/zoujingli/ip2region) |
| ip2region官网                                                | [link](https://ip2region.net/)                             |
| ip2region-github地址                                         | [link](https://github.com/lionsoul2014/ip2region)          |

### [ip2region 官网](https://ip2region.net/)

### 使用[zoujingli/](https://packagist.org/packages/zoujingli/)ip2region扩展包

**安装**

```php
composer require zoujingli/ip2region:^3.0
```

**简单使用**

```php
<?php

namespace app\common\service;

use app\common\service\abs\CommonService;

class IpService extends CommonService
{
    /**
     * 返回ip中文地址
     * @param $ip
     * @return array|string|null
     * @throws \Exception
     */
    public function getIpAddress($ip, $method = 'search')
    {
        $ret = null;
        try {
            $ret = ip2region($ip, $method) ?? "";
        } catch (\Exception $e) {
            return $ret;
        }
        return $ret;
    }
}
```

##  读取mp3音乐时长

### getID3

**安装**

```php
composer require james-heinrich/getid3
```

**本地文件读取时长**

```php
use getID3;

$getID3 = new getID3();
$filePath = 'audio/test.mp3';
$fileInfo = $getID3->analyze($filePath);

// 总秒数（浮点）
$seconds = $fileInfo['playtime_seconds'];
// 格式化 00:00:00
$h = floor($seconds / 3600);
$m = floor(($seconds % 3600) / 60);
$s = floor($seconds % 60);
$durationStr = sprintf("%02d:%02d:%02d", $h, $m, $s);

echo "总秒数：{$seconds}，时长：{$durationStr}";
```

