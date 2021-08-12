# 资料

| name              | url                                                          |
| ----------------- | ------------------------------------------------------------ |
| php-curl 句柄选项 | [link](https://www.php.net/manual/zh/function.curl-setopt.php) |
| php-curl          | [link](https://www.php.net/curl)                             |
|                   |                                                              |



# 代码示例

```shell
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

