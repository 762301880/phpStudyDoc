#  介绍

> 开发过程中遇到了一个问题，由于上传文件时存储在服务器当中的
>
> 由于采用了laravel还有tp框架一起开发,所以上传文件时候保存
>
> 到数据库就有了要求 **://路径.图片.jpg**这种，所以获取图片信息的时候
>
> 就需要自己判断是**http**请求还是**https**请求

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

