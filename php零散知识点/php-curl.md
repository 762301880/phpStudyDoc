# 资料

- 简介

> cURL 是一个利用***URL***语法规定来传输文件和数据的工具,支持很多协议,如HTTP、FTP、TELNET等，
>
> 我们可以利用curl来发送**GET**、**POST** 等请求
>
> **curl操作四步骤**
>
> 1. 初始化 $ch=curl_init();
> 2. 执行参数 curl_setop($ch,参数);
> 3. 执行 curl_exec($ch);
> 4. 关闭 curl_close($ch);  
>
> 输出错误信息：curl_error($ch);

- 参考地址

| name              | url                                                          |
| ----------------- | ------------------------------------------------------------ |
| php-curl 句柄选项 | [link](https://www.php.net/manual/zh/function.curl-setopt.php) |
| php-curl          | [link](https://www.php.net/curl)                             |
| 菜鸟教程-php_curl | [link](https://www.runoob.com/php/php-ref-curl.html)         |



# 代码示例

```php
# 上传逻辑
public function request_post($url = '', $post_data = array())
    {
        if (empty($url) || empty($post_data)) {
            return false;
        }
        $postUrl = $url;
        $curlPost = $post_data;
        $ch = curl_init();//初始化curl
        curl_setopt($ch, CURLOPT_URL, $postUrl);//抓取指定网页
        curl_setopt($ch, CURLOPT_HEADER, 0);//设置header
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);//要求结果为字符串且输出到屏幕上
        curl_setopt($ch, CURLOPT_POST, 1);//post提交方式
        curl_setopt($ch, CURLOPT_POSTFIELDS, $curlPost);
        $data = curl_exec($ch);//运行curl
        curl_close($ch);
        return $data;
    }
```

