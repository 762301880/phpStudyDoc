#  补充

## 沙盒环境注册

**点击控制台-拉到最底下点击沙盒测试环境**

> **点进去之后自行创建沙盒应用**

![image-20230918092233567](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20230918092233567.png)

#  统一设置环境

##  douyin.php

> 此文件是统一调用
>
> 此文件放在项目\config\douyin.php

```php
<?php

static $sandbox = true;//是否开启沙盒环境

//切换对应环境信息请自行更改
switch ($sandbox) {
    case true://沙盒环境下
        $url = 'https://open-sandbox.douyin.com';//沙盒环境请求地址
        $app_id = env('DOUYIN_SANDBOX_APPID', 'ttf63d8080690fb4fe01');
        $secret = env('DOUYIN_SANDBOX_SECRET', '0825b82da4cd2225a5fe4c6e23283e9c9341b483');
        break;
    case false://开发&正式环境下
        $url = 'developer.toutiao.com';//正式环境请求地址
        $app_id = env('DOUYIN_APPID', 'DOUYIN_APPID');
        $secret = env('DOUYIN_SECRET', 'DOUYIN_SECRET');
        break;
    default:
        //..............
}


return [
    'app_id' => $app_id, 
    'secret' => $secret,
    'url' => $url
];
```

## DouYinService.php

> 公用方法service层级封装

```php
<?php


namespace App\Services;


use Illuminate\Support\Facades\Cache;

class DouYinService
{
    protected $url = "";
    protected $app_id = "";
    protected $secret = "";

    public function __construct()
    {
        $this->app_id = config('douyin.app_id');
        $this->secret = config('douyin.secret');
        $this->url = config('douyin.url');
    }

    /**
     * 统一调用请求
     * @param string $url 请求地址
     * @param array $post_data 请求数据
     * @param array $data 其他参数
     * @return bool|string
     */
    protected static function curl_request($url = '', $post_data = [], $data = [])
    {
        $cookie = !empty($data['cookie']) ? $data['cookie'] : "";
        $ch = curl_init();//初始化curl
        curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, 5);//在尝试连接时等待的秒数。设置为0，则无限等待
        curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json;charset=utf-8'));
        curl_setopt($ch, CURLOPT_URL, $url);//需要获取的 URL 地址
        curl_setopt($ch, CURLOPT_HEADER, 0);//设置header param:1
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);//要求结果为字符串且输出到屏幕上
        curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false); //禁止 cURL 验证对等证书
        curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false); //是否检测服务器的域名与证书上的是否一致
        if (!empty($post_data)) { # 如果提交的参数请求不为空
            if (is_array($post_data)) $post_data = json_encode($post_data);
            curl_setopt($ch, CURLOPT_POST, 1);//post提交方式
            curl_setopt($ch, CURLOPT_POSTFIELDS, $post_data);//提交的参数
        }
        if (!empty($cookie)) { # 如果有cookie传递cookie
            curl_setopt($ch, CURLOPT_COOKIE, $cookie);
        }
        $data = curl_exec($ch);//运行curl
        curl_close($ch); # 关闭curl请求
        return $data;
    }
}
```



# 接口调用凭证

## [getAccessToken](https://developer.open-douyin.com/docs/resource/zh-CN/mini-app/develop/server/interface-request-credential/get-access-token)

```php
    /**
     * getAccessToken
     * https://developer.open-douyin.com/docs/resource/zh-CN/mini-app/develop/server/interface-request-credential/get-access-token
     * @return mixed|string
     * @throws \Psr\SimpleCache\InvalidArgumentException
     */
    public function getAccessToken()
    {
        //查询缓存并返回缓存token
        $cache_key = 'douying_access_token';//抖音token
        $cache_token = Cache::get($cache_key);//查询缓存token
        if ($cache_token) return $cache_token;


        //设置缓存并返回token
        $url = $this->url . '/api/apps/v2/token';
        $req_data = [
            'appid' => $this->app_id,
            'secret' => $this->secret,
            'grant_type' => 'client_credential'
        ];
        $res = json_decode(self::curl_request($url, $req_data), true);
        $access_token = $res['data']['access_token'] ?? "";//token
        $expires_in = $res['data']['expires_in'] ?? "";//过期时间
        Cache::set($cache_key, $access_token, $expires_in - 900);
        return $access_token;
    }
```

# 订阅消息

## [查询订阅消息模版库](https://developer.open-douyin.com/docs/resource/zh-CN/mini-app/develop/server/subscribe-notification/query-template-list)