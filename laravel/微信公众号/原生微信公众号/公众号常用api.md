# 一、资料

## 1.1 官网文档[地址](https://developers.weixin.qq.com/doc/offiaccount/Account_Management/Generating_a_Parametric_QR_Code.html)

```apl
https://developers.weixin.qq.com/doc/offiaccount/Account_Management/Generating_a_Parametric_QR_Code.html
```

# 二、获取Access token

- 官网`api`[地址](https://developers.weixin.qq.com/doc/offiaccount/Basic_Information/Get_access_token.html)

## 2.1 项目中使用代码示例

```php
    # 依赖注入以便quan 
    private $accessToken;
    public function __construct()
    {
        $this->accessToken = $this->getAccessToken();
    } 
    /**
     * 获取公众号的token
     * @return mixed
     * 接口文档地址:https://developers.weixin.qq.com/doc/offiaccount/Basic_Information/Get_access_token.html
     */
    public function getAccessToken()
    {
        $appID = Config::get('wechat.official_account.default.app_id');
        $appSECRET = Config::get('wechat.official_account.default.secret');
        $data = Http::get("https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=$appID&secret=$appSECRET")->json();
        return $data['access_token'];
    }
```

# 三 、生成带参数的二维码

## 3.1 官网`api`[地址](https://developers.weixin.qq.com/doc/offiaccount/Account_Management/Generating_a_Parametric_QR_Code.html)

```php
https://developers.weixin.qq.com/doc/offiaccount/Account_Management/Generating_a_Parametric_QR_Code.html
```

## 3.2项目中使用代码示例

```php
 public function getQrcode(Request $request)
    {
        $text = '{"expire_seconds": 604800, "action_name": "QR_SCENE", "action_info": {"scene": {"scene_id": 123}}}';
        $params = json_decode($text, true);#json数据转化为数组
        $data = Http::post("https://api.weixin.qq.com/cgi-bin/qrcode/create?access_token={$this->accessToken}", $params)->json();
        $ticket = $data['ticket'];
        $images = Http::get("https://mp.weixin.qq.com/cgi-bin/showqrcode?ticket=$ticket");
        return file_put_contents('qrcode.png', $images); # 将文件保存在本地laravel框架保存在public目录下
    }
```

# 四、 模板消息接口

- 项目[地址](https://developers.weixin.qq.com/doc/offiaccount/Message_Management/Template_Message_Interface.html)

```php
$json = [
            "touser" => "o7wV86RHxGwlG_y8fo5-SHd_muZo",
            "template_id" => "tCsqWfkM6g-tvzlEK4OdgYfRaW_xyIA7vcctJDDUFKw",
            "url" => "www.baidu.com",
            "miniprogram" => [
                "appid" => "",
                "pagepath" => ""
            ],
            "data" => [
                "name" => [
                    "value" => "尼古拉斯.赵四",
                    "color" => "#173177"
                ],
                "date" => [
                    "value" => date('Y-m-d H:i:s',time()),
                    "color" => "red"
                ]
            ]
        ];
        $api = "https://api.weixin.qq.com/cgi-bin/message/template/send?access_token={$this->accessToken}";
        $data = Http::post($api, $json)->json();
        dd($data);
```



