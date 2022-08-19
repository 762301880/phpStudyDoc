# 资料

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

## [1.4 微信测试号申请](https://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=sandbox/login)

```shell
    https://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=sandbox/login
```



# 获取Access token

- 官网`api`[地址](https://developers.weixin.qq.com/doc/offiaccount/Basic_Information/Get_access_token.html)

##  项目中使用代码示例

> 注意真实开发中获取**access_token**，一定要使用***redis***缓存去获取

```php
   use Illuminate\Support\Facades\Config;
   use Illuminate\Support\Facades\Http;
   use Illuminate\Support\Facades\Cache;
     # 依赖注入以便全局使用
    private $accessToken;
    private $appId;
    private $appSecret;

    public function __construct()
    {
        $this->appId = Config::get('wechat.official_account.default.app_id');
        $this->appSecret = Config::get('wechat.official_account.default.secret');
        $this->accessToken = $this->getAccessToken();
    }
    /**
     * 获取公众号的token
     * @return mixed
     * 接口文档地址:https://developers.weixin.qq.com/doc/offiaccount/Basic_Information/Get_access_token.html
     */
    public function getAccessToken()
    {
         /***************************推荐写法*************************************/
       $appId = $this->appId;
        $appSecret = $this->appSecret;
        $key = 'official_account_access_token';     # 记住这里的换成key不可以是access_token为了防止与小程序的缓存accessToken重合
        //如果缓存存在直接返回缓存中的token
        if (!empty(Cache::get($key))) {
            Log::debug('内存中的token为:' . Cache::get($key));
            return Cache::get($key);
        }
        //反之执行缓存
        Log::info('开始请求小程序accessToken');
        $data = json_decode(file_get_contents("https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=$appId&secret=$appSecret"), true);
        $accessToken = $data['access_token'] ?? "";
        Log::info('获取小程序accessToken成功:' . json_encode($data) . ',当前accessToken' . $accessToken);
        //设置缓存过期时间2小时-五分钟
        if (!empty($accessToken)) Cache::set($key, $accessToken, (60 * 60 * 2 - 60 * 5));
        return $accessToken;
        
        
        /*********************************原生写法*****************************************/
        # 或者原生写法
        $appID = "*******************";
        $appSECRET = "*******************";
        $url = "https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid={$this->appId}&secret={$this->appSecret}";
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

# 生成带参数的二维码

## 官网`api`[地址](https://developers.weixin.qq.com/doc/offiaccount/Account_Management/Generating_a_Parametric_QR_Code.html)

```php
https://developers.weixin.qq.com/doc/offiaccount/Account_Management/Generating_a_Parametric_QR_Code.html
```

## 项目中使用代码示例

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

# 素材管理

> 有空研究一下如何使用guzzle 上传 https://developers.weixin.qq.com/community/develop/doc/0004c64ae882285531a954c5156400

## 新增临时素材

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

- 补充方法

```php
# 可以采用curl上传，使用php 的exec 函数执行原生命令      
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
        $ret = exec("curl -F media=@$absolute_path_file 'https://api.weixin.qq.com/cgi-bin/media/upload?access_token={$this->accessToken}&type=image' ");
        unlink($absolute_path_file); //上传完成之后删除临时文件
        dd($ret);
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

### windows php-7.4 版本无法上传成功

> 不知道为什么上面的代码使用***linux***跑的很顺畅，但是切换到***windows***执行的时候
>
> 就是给你返回一个`""`空字符，

- 参考[资料](https://www.phpnanshen.com/article/92.html)

**解决示例**

[腾讯云**412**状态码介绍](https://cloud.tencent.com/developer/section/1190171)

微信官网有人提出的[意见](https://developers.weixin.qq.com/community/develop/doc/000a6ca7ae4988a4d9f949d4456800)

![1634031537(1).jpg](https://i.loli.net/2021/10/12/bsqmQoc7dizn5ah.png)

```php
# curl 设置helder打印出来可以看出是412 状态
/**
 * curl_setopt($curl, CURLOPT_HEADER, 1);//设置header 记得使用日志保存curl_exec()返回的结果
 */
[2021-10-12 07:26:55] local.DEBUG: HTTP/1.1 412 Precondition Failed
Connection: keep-alive
Date: Tue, 12-Oct-2021 07:26:54 GMT
Content-Length: 0

# 412 Precondition Failed
/**
 * 服务器在验证在请求的头字段中给出先决条件时，没能满足其中的一个或多个。
 * 这个状态码允许客户端在获取资源时在请求的元信息（请求头字段数据）中设置先决条件，
 * 以此避免该请求方法被应用到其希望的内容以外的资源上。
 * HTTP 412错误就是先决条件不满足，所以就是请求头的问题，
 * 对比PHP7.3.X和PHP7.4.X的请求头得出的结论，PHP7.3.X的请求头默认有Content-Length字段，
 * 且其值比文件的字节大小多198，并且请求头中不包含Transfer-Encoding字段。
 * 而PHP7.4.X的请求头中不包含Content-Length字段，所以要添加上，且默认包含Transfer-Encoding字段，所以要把该字段设置为空。
 */
# 解决方案 
# 参考1 https://www.zhihu.com/question/363042741/answer/1037650988
# 参考2 https://segmentfault.com/q/1010000021407039/a-1020000021870755?sort=created    
//1.需要在    $output = curl_exec($curl); 之前加上一行代码   
//文件路径可根据需求改为变量，而且还发现CURLFile的第三个参数，也就是a.jpg，不能用变量，只能用固定的字符串。
curl_setopt($curl,CURLOPT_HTTPHEADER,['Transfer-Encoding:','Content-Length:'.(filesize('文件的绝对路径')+198)]);
//2. 修改 上传函数 写死一个 a.jpg 参数
 'media' => new \CURLFile(realpath($file_name),'','a.jpg') # 如果是上传视频则修为改为 .mp4     
```

- 修改版本代码

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
                  'media' => new \CURLFile(realpath($file_name), '', 'a.jpg')
              );
          } else { # php 5.6以下使用
              $josn = array('media' => '@' . realpath($file_name));
          }
          $ret = $this->https_request($url, $josn, $absolute_path_file);
          unlink($absolute_path_file); //上传完成之后删除临时文件
          dd($ret);
      }
  
      public function https_request($url, $data = null, $absolute_path_file = null)
      {
          $curl = curl_init();
          curl_setopt($curl, CURLOPT_URL, $url);
          //curl_setopt($curl, CURLOPT_HEADER, 1);//设置header
          curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, FALSE);
          curl_setopt($curl, CURLOPT_SSL_VERIFYHOST, FALSE);
          if (!empty($data)) {
              curl_setopt($curl, CURLOPT_POST, 1);
              curl_setopt($curl, CURLOPT_POSTFIELDS, $data);
          }
          curl_setopt($curl, CURLINFO_HEADER_OUT, true);
          curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
          if (PHP_VERSION >= '7.4') {
              curl_setopt($curl, CURLOPT_HTTPHEADER, ['Transfer-Encoding:', 'Content-Length:' . (filesize($absolute_path_file) + 198)]);
          }
          $output = curl_exec($curl);
          Log::debug($output);
          Log::error(curl_getinfo($curl, CURLINFO_HEADER_OUT));
          curl_close($curl);
          return $output;
      }
  # 返回结果示例
  "{"type":"image","media_id":"Y4XvXKKlbtNyfDqR-OH_InMZHL0soJMf92NpJnhKp9bgg4EHgUbw3Dn02FvdpEds","created_at":1634031814,"item":[]}"
  ```

  

## 获取临时素材

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

##  [新增永久素材](https://developers.weixin.qq.com/doc/offiaccount/Asset_Management/Adding_Permanent_Assets.html)

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

## 获取永久素材

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

##  获取素材总数



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

## 获取素材列表

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

## 删除永久素材

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

## 修改永久图文素材

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

#  自定义菜单

- 资料

| name           | url                                                          |
| -------------- | ------------------------------------------------------------ |
| 官方- 接口文档 | [link](https://developers.weixin.qq.com/doc/offiaccount/Custom_Menus/Creating_Custom-Defined_Menu.html) |
| 参考资料       | [link](https://www.jb51.net/article/94175.htm)               |
|                |                                                              |

## 创建菜单

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

##  查询菜单

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

## 删除菜单

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

# 基础消息能力

## [接入](https://developers.weixin.qq.com/doc/offiaccount/Basic_Information/Access_Overview.html)

| 名称                   | 地址                                                         |
| ---------------------- | ------------------------------------------------------------ |
| 第三方博客参考         | [link](https://blog.csdn.net/timothy93bp/article/details/77650012)  [link](https://www.cnblogs.com/dreamzhiya/p/3790626.html) [link](https://www.cnblogs.com/myIvan/p/7228888.html) [link](https://www.zhihu.com/tardis/sogou/art/31123696) |
| 微信官方测试号申请地址 | [link](http://mp.weixin.qq.com/debug/cgi-bin/sandboxinfo?action=showinfo&t=sandbox/index) |
| php 示例代码下载       | [link](https://res.wx.qq.com/op_res/-serEQ6xSDVIjfoOHcX78T1JAYX-pM_fghzfiNYoD8uHVd3fOeC0PC_pvlg4-kmP) |

**首先我们在测试号中填写服务器配置**

> 这里我们填写**URL**(校验请求地址),**token**(可以自定义)

![1638251797(1).jpg](https://i.loli.net/2021/11/30/JnlMZL3VuEgK1T8.png)

**[接入指南](https://developers.weixin.qq.com/doc/offiaccount/Basic_Information/Access_Overview.html)**

> 之前一直不知道微信是怎么玩的果然想知道怎么玩还是要先查看别人是怎么玩的然后再从别人的写法中去分析
>
> 为什么要这样做

**接入代码示例**

| 参数      | 描述                                                         |
| :-------- | :----------------------------------------------------------- |
| signature | 微信加密签名，signature结合了开发者填写的token参数和请求中的timestamp参数、nonce参数。 |
| timestamp | 时间戳                                                       |
| nonce     | 随机数                                                       |
| echostr   | 随机字符串                                                   |

> 开发者通过检验signature对请求进行校验（下面有校验方式）。若确认此次GET请求来自微信服务器，请原样返回***echostr参数内容***，**则接入生效**，成为开发者成功，否则接入失败。加密/校验流程如下：
>
> 1）将token、timestamp、nonce三个参数进行字典序排序 2）将三个参数字符串拼接成一个字符串进行sha1加密 3）开发者获得加密后的字符串可与signature对比，标识该请求来源于微信

```php
 public function wechat()
    {
        if (!isset($_GET['echostr'])) {
            $this->message(); # 消息通知
        }else{
            $this->valid(); # 如果 $_GET['echostr'] 不存在的情况下校验接入服务器配置
        }
    }
    public function valid()
    {
        $echoStr = $_GET["echostr"];
        if($this->checkSignature()){
            echo $echoStr;
            exit;
        }
    }
    //检查签名
    private function checkSignature()
    {
        $signature = $_GET["signature"];
        $timestamp = $_GET["timestamp"];
        $nonce = $_GET["nonce"];
        $token = "yaoliuyang";
        $tmpArr = array($token, $timestamp, $nonce);
        sort($tmpArr, SORT_STRING);
        $tmpStr = implode($tmpArr);
        $tmpStr = sha1($tmpStr);
        if ($tmpStr == $signature) {
            return true;
        } else {
            return false;
        }
    }

    protected function message()
    {
        //get post data, May be due to the different environments
        $postStr = file_get_contents("php://input", 'r');//php:input
        //日志图片
        if (!empty($postStr)){
            libxml_disable_entity_loader(true);
            $postObj = simplexml_load_string($postStr, 'SimpleXMLElement', LIBXML_NOCDATA);
            $fromUsername = $postObj->FromUserName;
            $toUsername = $postObj->ToUserName;
            $keyword = trim($postObj->Content);
            $time = time();
            $textTpl = "<xml>
                           <ToUserName><![CDATA[%s]]></ToUserName>
                           <FromUserName><![CDATA[%s]]></FromUserName>
                           <CreateTime>%s</CreateTime>
                           <MsgType><![CDATA[%s]]></MsgType>
                           <Content><![CDATA[%s]]></Content>
                           <FuncFlag>0</FuncFlag>
                           </xml>";
            //订阅事件
            if($postObj->Event=="subscribe")
            {
                $msgType = "text";
                $contentStr = "欢迎关注";
                $resultStr = sprintf($textTpl, $fromUsername, $toUsername, $time, $msgType, $contentStr);
                echo $resultStr;
            }


            //语音识别
            if($postObj->MsgType=="voice"){
                $msgType = "text";
                $contentStr = trim($postObj->Recognition,"。");
                $resultStr = sprintf($textTpl, $fromUsername, $toUsername, $time, $msgType, $contentStr);
                echo  $resultStr;
            }

            //自动回复
            if(!empty( $keyword ))
            {
                $msgType = "text";
                $contentStr = "自动回复测试";
                $resultStr = sprintf($textTpl, $fromUsername, $toUsername, $time, $msgType, $contentStr);
                echo $resultStr;
            }else{
                echo "Input something...";
            }

        }else {
            echo "";
        }
    }
```



##  [接受普通消息](https://developers.weixin.qq.com/doc/offiaccount/Message_Management/Receiving_standard_messages.html)

> 微信公众平台开发者模式允许用户自己配置服务器，这样来自粉丝的信息，通过微信平台包装成 xml 格式，发送给后台服务器，后台服务器解析处理后，同样把信息包装成 xml 格式，通过微信平台，发送给用户。
> 微信平台和后台服务器直接是通过 xml 格式通信的（http POST），格式如下：

```php
<xml>
  <ToUserName><![CDATA[toUser]]></ToUserName>
  <FromUserName><![CDATA[fromUser]]></FromUserName>
  <CreateTime>1348831860</CreateTime>
  <MsgType><![CDATA[text]]></MsgType>
  <Content><![CDATA[this is a test]]></Content>
  <MsgId>1234567890123456</MsgId>
</xml>
    
# 因为 xml 格式的 post 数据包不是 php 默认识别的，所以不能使用 $_POST [] 直接取值，需要获取原始数据包，即使用如下语句处理接收到的数据包：
$msg = $GLOBALS[HTTP_RAW_POST_DATA];
$xmlObj = simplexml_load_string($msg, 'SimpleXMLElement', LIBXML_NOCDATA);
$msgType = $xmlObj->MsgType;
# Laravel 的话，在 Controller 中要使用 $msg = $request->getContent() 获取原始数据。
```



## 模板消息接口

> 项目[地址](https://developers.weixin.qq.com/doc/offiaccount/Message_Management/Template_Message_Interface.html)

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

# 微信网页开发

## [网页授权](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/Wechat_webpage_authorization.html)

**配置**

> 由于我是沙箱环境开发所以只需要在 [微信网页账号配置授权](https://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=sandbox/login)
>
> 这里需要加**http**

<img src="https://s2.loli.net/2022/01/04/cqESUfIpeDw2JQy.png" alt="1641282368(1).jpg" style="zoom:50%;" />

![1641282427(1).jpg](https://s2.loli.net/2022/01/04/7ebGcPZvRJnVs9S.png)

### [第一步：用户同意授权，获取code](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/Wechat_webpage_authorization.html#0)

> 这里url需要encode，并且需要携带http

```php
  public function webpageAuthorization()
    {
        $appId = $this->appId;
        # 需要跳转的授权地址
        $redirectUri = urlencode("http://81.69.231.252:1997/api/official_account/webpage_get_access_token");
        $url = "https://open.weixin.qq.com/connect/oauth2/authorize?appid={$appId}&redirect_uri={$redirectUri}&response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect";
        return $url;
    }
```

### [第二步：通过code换取网页授权access_token](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/Wechat_webpage_authorization.html#1)

```php
    public function webpageGetAccessToken(Request $request)
    {
        //$expirationTime = 3600 * 2;//过期时间2小时
        //$key = 'webPageAccessToken';
        # 如果不为空直接返回缓存token
        //$webPageAccessToken = \Cache::get($key);
        //if (!empty($webPageAccessToken)) return $webPageAccessToken;

        $code = $request->input('code');//填写第一步获取的code参数
        Log::debug("code值为:" . $code);
        $url = "https://api.weixin.qq.com/sns/oauth2/access_token?appid={$this->appId}&secret={$this->appSecret}&code={$code}&grant_type=authorization_code";
        $res = json_decode(file_get_contents($url), true);
        $accessToken = $res;
        #保存一份到缓存
        //\Cache::set($key, $accessToken, $expirationTime);
        Log::debug('accessToken为:' . $accessToken);
        return $accessToken;
    }
# 注意这里不能用缓存 因为code过期时间只有五分钟且唯一，如果开头用了缓存,下一个用户进来不管code是什么都会预先走到缓存里缓存两小时还没有过期所以必定会返回上一个用户的token信息好家伙 2022年8月12日因为这个问题排查了好几个小时特此记录
```

[ 第三步：刷新access_token（如果需要）](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/Wechat_webpage_authorization.html#2)

[第四步：拉取用户信息(需scope为 snsapi_userinfo)](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/Wechat_webpage_authorization.html#3)

```php
public function webpageGetUserinfo($access_token,$openid)
    {
        # token是上一步通过code换取网页授权access_token  里面的token还有 openid
        $url = "https://api.weixin.qq.com/sns/userinfo?access_token={$access_token}&openid={$openid}&lang=zh_CN";
        $res = json_decode(file_get_contents($url), true)
        dd($res);
    }
# 返回示例
array:9 [
  "openid" => "o7wV86RHxGwlG_y8fo5-SHd_muZo"
  "nickname" => "毒药"
  "sex" => 0
  "language" => ""
  "city" => ""
  "province" => ""
  "country" => ""
  "headimgurl" => "https://thirdwx.qlogo.cn/mmopen/vi_32/5ibQvCg79gytEmEXQV9PBdqqk0R5rEGe4CjhqZcjOcHayiaFiaaGkG8ncATQ8H05d12icpeUajK4PoibKEQIkMPNIqA/132"
  "privilege" => []
]
```

[附：检验授权凭证（access_token）是否有效](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/Wechat_webpage_authorization.html#4)

