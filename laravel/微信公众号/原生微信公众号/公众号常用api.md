# 一、资料

## 1.1 官网文档[地址](https://developers.weixin.qq.com/doc/offiaccount/Account_Management/Generating_a_Parametric_QR_Code.html)

```apl
https://developers.weixin.qq.com/doc/offiaccount/Account_Management/Generating_a_Parametric_QR_Code.html
```

## 1.2 w3school 微信公众号开发文档

```api
https://www.w3cschool.cn/weixinkaifawendang/
```

## 1.3  第三方博客参考

```php
https://blog.csdn.net/ljh101/article/details/108806075
```

# 二、获取Access token

- 官网`api`[地址](https://developers.weixin.qq.com/doc/offiaccount/Basic_Information/Get_access_token.html)

## 2.1 项目中使用代码示例

> 注意真实开发中获取**access_token**，一定要使用***redis***缓存去获取

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
        /***************************公共参数*************************************/
        # 调用config下面的配置 app_id、secret
            $appID = Config::get('wechat.official_account.default.app_id');
        $appSECRET = Config::get('wechat.official_account.default.secret');
        
         /***************************t*************************************/
        if (Cache::has('access_token') != false) {
            //如果缓存存在直接返回缓存中的token
            return Cache::get('access_token');
        }
        //反之执行缓存
        $data = json_decode(file_get_contents("https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=$appID&secret=$appSECRET"), true);
        if (isset($data['access_token'])) {
            //设置缓存过期时间2小时
            Cache::put('access_token', $data['access_token'], 60 * 60 * 2);
        }
        return $data['access_token'];
        
        /*********************************原生写法*****************************************/
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

### 代码示例

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
            $josn = array( # php5.6以上使用
                'media' => new \CURLFile(realpath($file_name))
            );
        } else { # php 5.6以下使用
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
        curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, FALSE); //禁止 cURL 验证对等证书
        curl_setopt($curl, CURLOPT_SSL_VERIFYHOST, FALSE); //是否检测服务器的域名与证书上的是否一致
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
```

- 返回结果示例

```php
array:4 [
  "type" => "image"
  "media_id" => "lISmUds-02cCRkjVsAfI3yxhbkYDSYQTnW_oQKlS-it7blBiQ9HVY1X80Ch5JnYq" # 返回的媒体id
  "created_at" => 1633399965
  "item" => []
]
```

### 遇到的bug

参考[资料](https://blog.csdn.net/weixin_40786663/article/details/99681571)

- 413 Request Entity Too Large

> 请求体太大,nginx配置问题
>
> **解决方案**
>
> 找到nginx配置文件，然后在配置文件中加入以下配置

```php
client_max_body_size 100m;
```

### windows 无法上传成功

> 不知道为什么上面的代码使用***linux***跑的很顺畅，但是切换到***windows***执行的时候
>
> 就是给你返回一个`""`空字符，

- 参考[资料](https://www.phpnanshen.com/article/92.html)

**解决示例**

```php
# curl 设置helder打印出来可以看出是412 状态
/**
 * curl_setopt($curl, CURLOPT_HEADER, 1);//设置header 记得使用日志保存curl_exec()返回的结果
 */
[2021-10-12 07:26:55] local.DEBUG: HTTP/1.1 412 Precondition Failed
Connection: keep-alive
Date: Tue, 12-Oct-2021 07:26:54 GMT
Content-Length: 0
# 具体原因有待分析待日后补充    
```



## 5.2 获取临时素材

- 资料

| 名称                  | 地址                                                         |
| --------------------- | ------------------------------------------------------------ |
| 官方-获取临时素材地址 | [link](https://developers.weixin.qq.com/doc/offiaccount/Asset_Management/Get_temporary_materials.html) |
|                       |                                                              |

### 代码示例

- [base64图片转换工具](http://tool.chinaz.com/tools/imgtobase/)
- 参考[博客](https://www.cnblogs.com/oldinaction/p/5167465.html)

```php
 public function getTemporaryMaterial($mediaId)
    {
        $url = "https://api.weixin.qq.com/cgi-bin/media/get?access_token={$this->accessToken}&media_id={$mediaId}";
        $data = $this->curl_get_file($url);
        dd(file_put_contents('a.jpg', $data));# 写入到本地
        //或者 加密成base64不含头部信息的图片直接传给前端处理 例如 data:image/jpeg;base64,加密的base64编码
        dd(base64_encode($data));
    }

    public function curl_get_file($url)
    {
        $ch = curl_init($url);
        curl_setopt($ch, CURLOPT_HEADER, 0);
        curl_setopt($ch, CURLOPT_NOBODY, 0);                //只取body头
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);//curl_exec执行成功后返回执行的结果；不设置的话，curl_exec执行成功则返回true
        $output = curl_exec($ch);
        curl_close($ch);
        return $output;
    }
```

## 5.3  [新增永久素材](https://developers.weixin.qq.com/doc/offiaccount/Asset_Management/Adding_Permanent_Assets.html)

###  新增永久图文素材

 [参考资料](https://blog.csdn.net/qq_40011533/article/details/106096911)

- 接口调用请求说明

```php
/**
* 调用示例
* http请求方式: POST，https协议 https://api.weixin.qq.com/cgi-bin/material/add_news?access_token=ACCESS_TOKEN
*/
{
    "articles": [{
     "title": TITLE,
    "thumb_media_id": THUMB_MEDIA_ID,
    "author": AUTHOR,
    "digest": DIGEST,
    "show_cover_pic": SHOW_COVER_PIC(0 / 1),
    "content": CONTENT,
    "content_source_url": CONTENT_SOURCE_URL,
    "need_open_comment":1,
    "only_fans_can_comment":1
},
    //若新增的是多图文素材，则此处应还有几段articles结构
]
}
```



**参数说明**

| 参数                  | 是否必须 | 说明                                                         |
| :-------------------- | :------- | :----------------------------------------------------------- |
| title                 | 是       | 标题                                                         |
| thumb_media_id        | 是       | 图文消息的封面图片素材id（必须是永久mediaID）                |
| author                | 否       | 作者                                                         |
| digest                | 否       | 图文消息的摘要，仅有单图文消息才有摘要，多图文此处为空。如果本字段为没有填写，则默认抓取正文前54个字。 |
| show_cover_pic        | 是       | 是否显示封面，0为false，即不显示，1为true，即显示            |
| content               | 是       | 图文消息的具体内容，支持HTML标签，必须少于2万字符，小于1M，且此处会去除JS,涉及图片url必须来源 "上传图文消息内的图片获取URL"接口获取。外部图片url将被过滤。 |
| content_source_url    | 是       | 图文消息的原文地址，即点击“阅读原文”后的URL                  |
| need_open_comment     | 否       | Uint32 是否打开评论，0不打开，1打开                          |
| only_fans_can_comment | 否       | Uint32 是否粉丝才可评论，0所有人可评论，1粉丝才可评论        |

**代码示例**

```php
public function addForeverMaterial(Request $request)
    {
        $url = "https://api.weixin.qq.com/cgi-bin/material/add_news?access_token={$this->accessToken}";
        $articles = [
            'articles' => [
                [
                    "title" => '2131',
                    "thumb_media_id" => 'c_m-4fqktopH35sgQfmzqfPyjoN0xZJ0RXMyQPea8tE',
                    "author" => 'yaoliuyang',
                    "digest" => '1322233333333333333333',
                    "show_cover_pic" => 1,
                    "content" => '13222333333333333333244444444444444333',
                    "content_source_url" => '1322233333333333333333',
                    "need_open_comment" => 1,
                    "only_fans_can_comment" => 1
                ]
            ]
        ];
        $res = $this->https_request($url, json_encode($articles));
        dd($res);
    }
 # curl请求
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
```

**结果示例**

```php
array:2 [
  "media_id" => "c_m-4fqktopH35sgQfmzqbfEgBzhG24VZDQqMBo5iMY"
  "item" => []
]
```



### 上传图文消息内的图片获取URL

>本接口所上传的图片不占用公众号的素材库中图片数量的100000个的限制。图片仅支持jpg/png格式，大小必须在1MB以下
>
>唯一不同的就是这个接口会返回iu图片的***url***并不会返回图片的**media_id**

```php
 public function addForeverMaterial(Request $request)
    {
        $url = "https://api.weixin.qq.com/cgi-bin/media/uploadimg?access_token={$this->accessToken}";
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
            $josn = array( # php5.6以上使用
                'media' => new \CURLFile(realpath($file_name))
            );
        } else { # php 5.6以下使用
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
```

**返回结果示例**

```php
array:1 [
  "url" => "http://mmbiz.qpic.cn/mmbiz_jpg/yuXMG6DMxJ4Iow46gW2fhhdT1ia2nDHQibicvRt41EbdQ1TEDmcvaiaVPyoN8Yrl6lNxrqOeSaIZSSpsjoOWPaxpvg/0"
]
```



###  新增其他类型永久素材

参考[资料](https://www.jb51.net/article/110925.htm)

**代码示例**

```php
# 上传图片
  public function addForeverMaterial(Request $request)
    {
        $url = "https://api.weixin.qq.com/cgi-bin/material/add_material?access_token={$this->accessToken}&type=image";
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
            $josn = array( # php5.6以上使用
                'media' => new \CURLFile(realpath($file_name))
            );
        } else { # php 5.6以下使用
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
# 上传视频
/**
 * 上传视频时候的数据封装
 * 在上传视频素材时需要POST另一个表单，表单为description，包含素材的描述信息，内容格式为JSON，格式如下：
 */
 $josn = array( # php5.6以上使用
                'media' => new \CURLFile(realpath($file_name)),
                'description' => json_encode([
                    'title' => '视频标题', # 可配置化
                    'introduction' => '视频描述' # 可配置化
                ])
    );
```

**返回结果示例**

```php
# 上传永久图片返回结果示例
array:3 [
  "media_id" => "c_m-4fqktopH35sgQfmzqekI_ZVWxGyib24e1ZLFy3c"
  "url" => "http://mmbiz.qpic.cn/mmbiz_jpg/yuXMG6DMxJ4Fc7VhKKibyBRMll6RNK
  s8RsPWvXtvgQWSa4dZsk62YJZicic9OJ2VxyMXXDmpqV3ib2K8VnWb4Sp9kg/0?wx_fmt=jpeg"
  "item" => []
]
#  上传视频返回示例
array:2 [
  "media_id" => "c_m-4fqktopH35sgQfmzqYO7OCBtCZLKO7mGivJplyQ"
  "item" => []
]    
```

## 5.4 获取永久素材

>在新增了永久素材后，开发者可以根据media_id通过本接口下载永久素材。
>
>公众号在公众平台官网素材管理模块中新建的永久素材，可通过"获取素材列表"获知素材的media_id。
>
>请注意：临时素材无法通过本接口获取

**接口请求说明**

>http请求方式: POST,https协议 https://api.weixin.qq.com/cgi-bin/material/get_material?access_token=ACCESS_TOKEN

`请求示例`

```php
{
  "media_id":MEDIA_ID
}
```

`调用参数说明`

| 参数         | 是否必须 | 说明                   |
| :----------- | :------- | :--------------------- |
| access_token | 是       | 调用接口凭证           |
| media_id     | 是       | 要获取的素材的media_id |

**代码示例**

```php
 public function getForeverMaterial()
    {
       $url="https://api.weixin.qq.com/cgi-bin/material/get_material?access_token={$this->accessToken}";
       $data=['media_id'=>'c_m-4fqktopH35sgQfmzqbfEgBzhG24VZDQqMBo5iMY'];
       $res=$this->https_request($url,json_encode($data));
       dd($res);
    }
    # curl请求
    /**
     * 注意返回的结果最好不要用json_decode() 包裹
     * 如果返回的是一个**图片的二进制**则直接返回为null
     */
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
        curl_close($curl);
        return $output;
    }
```

**返回示例**

```php
# 1. 图文素材返回
array:3 [
  "news_item" => array:1 [
    0 => array:11 [
      "title" => "2131"
      "author" => "yaoliuyang"
      "digest" => "1322233333333333333333"
      "content" => "13222333333333333333244444444444444333"
      "content_source_url" => "http://1322233333333333333333"
      "thumb_media_id" => "c_m-4fqktopH35sgQfmzqfPyjoN0xZJ0RXMyQPea8tE"
      "show_cover_pic" => 1
      "url" => "http://mp.weixin.qq.com/s?__biz=MzkxNzIxOTMxMQ==&mid=100000010&idx=1&sn=32d48530a6b8a659df2a7a8fe704b2b9&chksm=4142b38a76353a9c7d1b0f5604559fc60bcd51a9d713a198cb1e442977984c4162bc824d014a#rd"
      "thumb_url" => "http://mmbiz.qpic.cn/mmbiz_jpg/yuXMG6DMxJ4Fc7VhKKibyBRMll6RNKs8RsPWvXtvgQWSa4dZsk62YJZicic9OJ2VxyMXXDmpqV3ib2K8VnWb4Sp9kg/0?wx_fmt=jpeg"
      "need_open_comment" => 1
      "only_fans_can_comment" => 1
    ]
  ]
  "create_time" => 1633542202
  "update_time" => 1633542203
]
# 2. 其他素材例如图片返回的是二进制(请自行保存到本地或者base64加密返回)    
```

##  5.5 获取素材总数



```php
 public function getMaterialCount()
    {
       $url = "https://api.weixin.qq.com/cgi-bin/material/get_materialcount?access_token={$this->accessToken}";
       $res=$this->curl_get($url);
       dd($res);
    }
 public function curl_get($url)
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

**结果示例**

```php
array:4 [
  "voice_count" => 0 # 语音总数量
  "video_count" => 3 # 视频总数量
  "image_count" => 4 # 图片总数量
  "news_count" => 1  # 图文总数量
]
```

## 5.6 获取素材列表

接口调用请求说明

`http请求方式: POST https://api.weixin.qq.com/cgi-bin/material/batchget_material?access_token=ACCESS_TOKEN`

调用示例

```php
{
    "type":TYPE,
    "offset":OFFSET,
    "count":COUNT
}
```

参数说明

| 参数   | 是否必须 | 说明                                                         |
| :----- | :------- | :----------------------------------------------------------- |
| type   | 是       | 素材的类型，图片（image）、视频（video）、语音 （voice）、图文（news） |
| offset | 是       | 从全部素材的该偏移位置开始返回，0表示从第一个素材 返回       |
| count  | 是       | 返回素材的数量，取值在1到20之间                              |

- 代码示例

```php
public function getMaterialList()
    {
        $url = "https://api.weixin.qq.com/cgi-bin/material/batchget_material?access_token={$this->accessToken}";
        $data = [
            'type' => 'image',
            'offset' => 0,
            'count' => 20
        ];
        $res = $this->https_request($url,json_encode($data));
        dd(json_decode($res,true));
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
        curl_close($curl);
        return $output;
    }

```

**结果示例**

```php
array:3 [
  "item" => array:4 [
    0 => array:5 [
      "media_id" => "c_m-4fqktopH35sgQfmzqWO2B49BAQdw2fyHTJYB9qE"
      "name" => "/home/yaoliuyang/公共的/phpProject/laravel_study/public/615e5fd9516899.jpg"
      "update_time" => 1633574876
      "url" => "https://mmbiz.qpic.cn/mmbiz_jpg/yuXMG6DMxJ4Iow46gW2fhhdT1ia2nDHQibicvRt41EbdQ1TEDmcvaiaVPyoN8Yrl6lNxrqOeSaIZSSpsjoOWPaxpvg/0?wx_fmt=jpeg"
      "tags" => []
    ]
    1 => array:5 [
      "media_id" => "c_m-4fqktopH35sgQfmzqfPyjoN0xZJ0RXMyQPea8tE"
      "name" => "/home/yaoliuyang/公共的/phpProject/laravel_study/public/615d80cf462bc9.jpg"
      "update_time" => 1633517790
      "url" => "https://mmbiz.qpic.cn/mmbiz_jpg/yuXMG6DMxJ4Fc7VhKKibyBRMll6RNKs8RsPWvXtvgQWSa4dZsk62YJZicic9OJ2VxyMXXDmpqV3ib2K8VnWb4Sp9kg/0?wx_fmt=jpeg"
      "tags" => []
    ]
    2 => array:5 [
      "media_id" => "c_m-4fqktopH35sgQfmzqUOyJapzDZ2t8-RNofcaNvM"
      "name" => "/home/yaoliuyang/公共的/phpProject/laravel_study/public/615d7bac37d7f9.jpg"
      "update_time" => 1633516540
      "url" => "https://mmbiz.qpic.cn/mmbiz_jpg/yuXMG6DMxJ4Fc7VhKKibyBRMll6RNKs8RsPWvXtvgQWSa4dZsk62YJZicic9OJ2VxyMXXDmpqV3ib2K8VnWb4Sp9kg/0?wx_fmt=jpeg"
      "tags" => []
    ]
    3 => array:5 [
      "media_id" => "c_m-4fqktopH35sgQfmzqekI_ZVWxGyib24e1ZLFy3c"
      "name" => "/home/yaoliuyang/公共的/phpProject/laravel_study/public/615d5f11a12cb9.jpg"
      "update_time" => 1633509152
      "url" => "https://mmbiz.qpic.cn/mmbiz_jpg/yuXMG6DMxJ4Fc7VhKKibyBRMll6RNKs8RsPWvXtvgQWSa4dZsk62YJZicic9OJ2VxyMXXDmpqV3ib2K8VnWb4Sp9kg/0?wx_fmt=jpeg"
      "tags" => []
    ]
  ]
  "total_count" => 4
  "item_count" => 4
]
```

## 5.7 删除永久素材

`http请求方式: POST https://api.weixin.qq.com/cgi-bin/material/del_material?access_token=ACCESS_TOKEN`

调用示例

```json
{
  "media_id":MEDIA_ID
}
```

参数说明

| 参数         | 是否必须 | 说明                   |
| :----------- | :------- | :--------------------- |
| access_token | 是       | 调用接口凭证           |
| media_id     | 是       | 要获取的素材的media_id |

- 代码示例

```php
public function deleteForeverMaterial()
    {
        $url = "https://api.weixin.qq.com/cgi-bin/material/del_material?access_token={$this->accessToken}";
        $data = [
            "media_id"=>'c_m-4fqktopH35sgQfmzqWO2B49BAQdw2fyHTJYB9qE' # 永久素材id可配置化,这里实验写死
        ];
        $res = $this->https_request($url,json_encode($data));
        dd(json_decode($res,true));
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
        curl_close($curl);
        return $output;
    }
```

**结果示例**

```php
array:2 [
  "errcode" => 0
  "errmsg" => "ok"
]
```

## 5.8 修改永久图文素材

接口调用请求说明

`http请求方式: POST https://api.weixin.qq.com/cgi-bin/material/update_news?access_token=ACCESS_TOKEN`

调用示例

```php
{
  "media_id":MEDIA_ID,
  "index":INDEX,
  "articles": {
       "title": TITLE,
       "thumb_media_id": THUMB_MEDIA_ID,
       "author": AUTHOR,
       "digest": DIGEST,
       "show_cover_pic": SHOW_COVER_PIC(0 / 1),
       "content": CONTENT,
       "content_source_url": CONTENT_SOURCE_URL
    }
}
```

参数说明

| 参数               | 是否必须 | 说明                                                         |
| :----------------- | :------- | :----------------------------------------------------------- |
| media_id           | 是       | 要修改的图文消息的id                                         |
| index              | 是       | 要更新的文章在图文消息中的位置（多图文消息时，此字段才有意义），第一篇为0 |
| title              | 是       | 标题                                                         |
| thumb_media_id     | 是       | 图文消息的封面图片素材id（必须是永久mediaID）                |
| author             | 是       | 作者                                                         |
| digest             | 是       | 图文消息的摘要，仅有单图文消息才有摘要，多图文此处为空       |
| show_cover_pic     | 是       | 是否显示封面，0为false，即不显示，1为true，即显示            |
| content            | 是       | 图文消息的具体内容，支持HTML标签，必须少于2万字符，小于1M，且此处会去除JS |
| content_source_url | 是       | 图文消息的原文地址，即点击“阅读原文”后的URL                  |

- 代码示例

```php
public function updateForeverMaterial()
    {
        $url = "https://api.weixin.qq.com/cgi-bin/material/update_news?access_token={$this->accessToken}";
        $data = [
            "media_id" => 'c_m-4fqktopH35sgQfmzqbfEgBzhG24VZDQqMBo5iMY',
            'index' => 0,
            'articles' => [
                "title" => '标题',
                "thumb_media_id" => 'c_m-4fqktopH35sgQfmzqfPyjoN0xZJ0RXMyQPea8tE',
                "author" => '作者',
                "digest" => '图文消息的摘要',
                "show_cover_pic" => 1,
                "content" => '图文消息的具体内容',
                "content_source_url" => '图文消息的原文地址'
            ]
        ];
        $res = $this->https_request($url, json_encode($data));
        dd(json_decode($res, true));
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
        curl_close($curl);
        return $output;
    }
```

**返回结果示例**

```php
array:2 [
  "errcode" => 0
  "errmsg" => "ok"
]
```

# 六 自定义菜单

- 资料

| name           | url                                                          |
| -------------- | ------------------------------------------------------------ |
| 官方- 接口文档 | [link](https://developers.weixin.qq.com/doc/offiaccount/Custom_Menus/Creating_Custom-Defined_Menu.html) |
| 参考资料       | [link](https://www.jb51.net/article/94175.htm)               |
|                |                                                              |

## 6.1 创建菜单

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

## 6.2 查询菜单

**代码示例**

```php
public function queryMenu()
    {
        $url = "https://api.weixin.qq.com/cgi-bin/get_current_selfmenu_info?access_token={$this->accessToken}";
        $res = $this->curl_get($url);
        dd($res);
    }
    # curl请求 
    public function curl_get($url)
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

**返回结果示例**

```php
array:2 [
  "is_menu_open" => 1
  "selfmenu_info" => array:1 [
    "button" => array:2 [
      0 => array:3 [
        "type" => "click"
        "name" => "今日歌曲"
        "key" => "V1001_TODAY_MUSIC"
      ]
      1 => array:2 [
        "name" => "菜单"
        "sub_button" => array:1 [
          "list" => array:2 [
            0 => array:3 [
              "type" => "view"
              "name" => "搜索"
              "url" => "http://www.soso.com/"
            ]
            1 => array:3 [
              "type" => "click"
              "name" => "赞一下我们"
              "key" => "V1001_GOOD"
            ]
          ]
        ]
      ]
    ]
  ]
]
```

## 6.3 删除菜单

>使用接口创建自定义菜单后，开发者还可使用接口删除当前使用的自定义菜单。
>
>另请注意，在个性化菜单时，调用此接口会删除默认菜单及全部个性化菜单。

**代码示例**

```php
 public function deleteMenu()
    {
        # get 请求
        $url = "https://api.weixin.qq.com/cgi-bin/menu/delete?access_token={$this->accessToken}";
        $res = $this->curl_get($url);
        dd($res);
    }

    public function curl_get($url)
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

**结果示例**

```php
array:2 [
  "errcode" => 0
  "errmsg" => "ok"
]
```

