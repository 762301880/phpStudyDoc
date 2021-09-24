# 代码示例

```php
<?php

if (!function_exists('getUrlPrefix')) {
    /**
     * 获取http请求前缀
     * @return false|string
     */
    function getUrlPrefix()
    {
        $url = request()->domain();
        $str_num_start = strpos($url, 'h');//查询冒号开始出现的位置(从0开始)
        //判断是http or https
        if (strpos($url, 'https')) {
            $str_num_end = strpos($url, 'https') + 6;//查询com的m结束位置(从0开始) 如果https存在返回https;
        } else {
            $str_num_end = strpos($url, 'http') + 4;//查询com的m结束位置(从0开始) http
        }
        //return http: or https:
        return substr($url, $str_num_start, $str_num_end).':';
    }

}
```

