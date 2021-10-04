# 一、资料

## 1.1 官网文档[地址](https://developers.weixin.qq.com/doc/offiaccount/Account_Management/Generating_a_Parametric_QR_Code.html)

```apl
https://developers.weixin.qq.com/doc/offiaccount/Account_Management/Generating_a_Parametric_QR_Code.html
```

## 1.2 w3school 微信公众号开发文档

```api
https://www.w3cschool.cn/weixinkaifawendang/
```

# 二、获取Access token

- 官网`api`[地址](https://developers.weixin.qq.com/doc/offiaccount/Basic_Information/Get_access_token.html)

## 2.1 项目中使用代码示例

```php
   use Illuminate\Support\Facades\Config;
   use Illuminate\Support\Facades\Http;
   # 依赖注入以便全局使用
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
        # 调用config下面的配置 app_id、secret
        $appID = Config::get('wechat.official_account.default.app_id');
        $appSECRET = Config::get('wechat.official_account.default.secret');
        $data = Http::get("https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=$appID&secret=$appSECRET")->json();
        return $data;
        # 或者原生写法
        $appID = "*******************";
        $appSECRET = "*******************";
        $url = "https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid={$appID}&secret={$appSECRET}";
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch,CURLOPT_RETURNTRANSFER,true);
        $data = curl_exec($ch);
        curl_close($ch);
        return json_decode($data,true);  
    }
# 返回结果示例
array:2 [▼
  "access_token" => "49_DnwYlC9x4f3sHyOLFW7b3QDsahJEfDIB12FNJKNQETc_vSzCR-vj46U5kPW5BVXrmf5NiYgiVjAJ_EvaZQAjDpOPUPsxOj_V1nA1oyo6FlongLOt4fnxIm78naepp3-tc4HWWkPVYdS2rNeUDCEaAIAYXZ"
  "expires_in" => 7200
]
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

# 五 、素材管理

## 5.1 新增临时素材

- 先采用curl上传实验

```shell
# 将图片放在电脑桌面采用curl上传实验-可以看出官方提供的接口是ok的
Administrator@PC-20200506AVAU MINGW64 ~/Desktop
$  curl -F media=@a.jpg "https://api.weixin.qq.com/cgi-bin/media/upload?access_token=你的token&type=image"
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 68175  100   128  100 68047    229   119k --:--:-- --:--:-- --:--:--  119k{"type":"image","media_id":"lJP_1YgbiXdIJj8AIdlmmvgcnASjHe_yA5fvTM9FLl-ENTHRplWKyrJJMsuD48Zr","created_at":1631690891,"item":[]}
```

- laravel代码

> 注意事项 上传图片需要绝对路径 例如 `C:/etc/www/laravel_study/public/a.jpg` 
>
> 可以将图片上传至框架目录然后再取得绝对路径

- postman上传

<img src="https://i.loli.net/2021/09/30/vWmJ6CdSyYuNt1b.png" alt="1632982890.jpg" style="zoom: 67%;" />

## 5.2代码示例

```php
 public function addTemporaryMaterial(Request $request)
    {
        $url = "https://api.weixin.qq.com/cgi-bin/media/upload?access_token=" . $this->getAccessToken() . "&type=image";
        $file_name = uniqid() . $request->file('media')->getClientOriginalName();//设置唯一的上传图片
        $path = public_path('/');//设置上传路径
        $absolute_path_file = $path . '/' . $file_name;//图片全路径,绝对路径
        $request->file('media')->move($path, $file_name);//转移文件到public目录下
        //判断文件是否存在
        if (!file_exists($absolute_path_file)) {
            return response()->json([
                'msg' => '文件没有上传成功',
                'data' => [],
                'code' => '5000'
            ]);
        }
        if (class_exists('\CURLFile')) {
            $josn = array(
                'media' => new \CURLFile(realpath($file_name)); # 5.6 之后使用CURLFile
            );
        } else {
            $josn = array('media' => '@' . realpath($file_name));
        }
        $ret = $this->https_request($url, $josn);
        unlink($absolute_path_file); //上传完成之后删除临时文件
        dd($ret);
    }

    public function https_request($url, $data = null)
    {
        $curl = curl_init();
        curl_setopt($curl, CURLOPT_URL, $url);
        curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, FALSE);
        curl_setopt($curl, CURLOPT_SSL_VERIFYHOST, FALSE);
        if (!empty($data)) {
            curl_setopt($curl, CURLOPT_POST, 1);
            curl_setopt($curl, CURLOPT_POSTFIELDS, $data);
        }
        curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
        $output = curl_exec($curl);
        $error = curl_error($curl);
        curl_close($curl);
        return json_decode($output, true);
    }
# 返回结果示例
array:4 [
  "type" => "image"
  "media_id" => "AAYIdVzqiTxbUHFMZ07A5ZgaHEZhfd900Y0h-CD_uzl06rbDP0CKuTTZ4-x-uUiu"
  "created_at" => 1633341836
  "item" => []
]
```



# 六 新增自定义菜单

- 资料

| name           | url                                                          |
| -------------- | ------------------------------------------------------------ |
| 官方- 接口文档 | [link](https://developers.weixin.qq.com/doc/offiaccount/Custom_Menus/Creating_Custom-Defined_Menu.html) |
| 参考资料       | [link](https://www.jb51.net/article/94175.htm)               |
|                |                                                              |

## 6.1 代码示例

```php
  public function createMenu()
    {
        $jsonmenu = '{ 
  "button":[ 
  { 
   "name":"天气预报", 
   "sub_button":[ 
   { 
    "type":"click", 
    "name":"北京天气", 
    "key":"天气北京"
   }, 
   { 
    "type":"click", 
    "name":"上海天气", 
    "key":"天气上海"
   }, 
   { 
    "type":"click", 
    "name":"广州天气", 
    "key":"天气广州"
   }, 
   { 
    "type":"click", 
    "name":"深圳天气", 
    "key":"天气深圳"
   }, 
   { 
    "type":"view", 
    "name":"本地天气", 
    "url":"http://m.hao123.com/a/tianqi"
   }] 
   
  
  }, 
  { 
   "name":"瑞雪", 
   "sub_button":[ 
   { 
    "type":"click", 
    "name":"公司简介", 
    "key":"company"
   }, 
   { 
    "type":"click", 
    "name":"趣味游戏", 
    "key":"游戏"
   }, 
   { 
    "type":"click", 
    "name":"撒旦", 
    "key":"笑话"
   }] 
   
  
  }] 
}';
        $url = "https://api.weixin.qq.com/cgi-bin/menu/create?access_token=" . $this->accessToken;
        $result = $this->https_request($url, $jsonmenu);
        dd($result);
    }

    public function https_request($url, $data = null)
    {
        $curl = curl_init();
        curl_setopt($curl, CURLOPT_URL, $url);
        curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, FALSE);
        curl_setopt($curl, CURLOPT_SSL_VERIFYHOST, FALSE);
        if (!empty($data)) {
            curl_setopt($curl, CURLOPT_POST, 1);
            curl_setopt($curl, CURLOPT_POSTFIELDS, $data);
        }
        curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);
        $output = curl_exec($curl);
        curl_close($curl);
        return $output;
    }
```

