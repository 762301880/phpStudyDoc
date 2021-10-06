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
# http请求方式: POST，https协议 https://api.weixin.qq.com/cgi-bin/material/add_news?access_token=ACCESS_TOKEN

# 调用示例

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

