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

# 常用参数

> 使用**curl  curl_setop()**函数的时候
>
> 我们要设置参数这里理解一下常用的函数

```php
# 传递需要请求的url 地址
CURLOPT_URL   //需要获取的 URL 地址，也可以在curl_init() 初始化会话的时候。
CURLOPT_RETURNTRANSFER    // true 将curl_exec()获取的信息以字符串返回，而不是直接输出。
# 设置头信息    
CURLOPT_HEADER   //启用时会将头文件的信息作为数据流输出。
CURLOPT_POST    //true 时会发送 POST 请求，类型为：application/x-www-form-urlencoded，是 HTML 表单提交时最常见的一种。

CURLOPT_POSTFIELDS   //全部数据使用HTTP协议中的 "POST" 操作来发送。 要发送文件，在文件名前面加上@前缀并使用完整路径。 文件类型可在文件名后以 ';type=mimetype' 的格式指定。 这个参数可以是 urlencoded 后的字符串，类似'para1=val1&para2=val2&...'，也可以使用一个以字段名为键值，字段数据为值的数组。 如果value是一个数组，Content-Type头将会被设置成multipart/form-data。 从 PHP 5.2.0 开始，使用 @ 前缀传递文件时，value 必须是个数组。 从 PHP 5.5.0 开始, @ 前缀已被废弃，文件可通过 CURLFile 发送。 设置 CURLOPT_SAFE_UPLOAD 为 true 可禁用 @ 前缀发送文件，以增加安全性。 

CURLOPT_SSL_VERIFYPEER  //false 禁止 cURL 验证对等证书（peer's certificate）。要验证的交换证书可以在 CURLOPT_CAINFO 选项中设置，或在 CURLOPT_CAPATH中设置证书目录。     自cURL 7.10开始默认为 true。从 cURL 7.10开始默认绑定安装。
   
    
```

# 代码示例

> 注意返回的结果最好不要用json_decode() 包裹
>
> 如果返回的是一个**图片的二进制**则直接返回为null

```php
# post请求
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
        curl_setopt($ch, CURLOPT_POSTFIELDS, $curlPost);//提交的参数
        $data = curl_exec($ch);//运行curl
        curl_close($ch);
        return $data;
    }
    # get请求
    public function request_get($url)
    {
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_HEADER, 0);
        curl_setopt($ch, CURLOPT_NOBODY, 0);                //只取body头
        if (!empty($data)) {
            curl_setopt($ch, CURLOPT_POST, 1);
            curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
        }
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);//curl_exec执行成功后返回执行的结果；不设置的话，curl_exec执行成功则返回true
        $output = curl_exec($ch);
        curl_close($ch);
        return json_decode($output, true);
    }
```

