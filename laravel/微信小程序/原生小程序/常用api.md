#  [接口调用凭证](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/access-token/auth.getAccessToken.html)

## [getAccessToken](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/access-token/auth.getAccessToken.html)

**代码示例**

> **注意事项(具体注意事项请查阅官方文档)**
>
> 1. **重复获取将导致上次获取的access_token失效(线上，测试重复调用)**
>
>    即线上,测试环境会出现这种情况,例如已经上线的功能发送模板消息,然后测试也发了一个模板消息,由于线上还有测试的token都是同一个密钥
>
>    调用的所以这边(线下)调用，会导致那边(线上)失效  新老token五分钟内可用(**建议使用中控服务器可以保证线上或测试调用的都是同一个token**)

```php
    /*
     * 返回AccessToken
     * 文档地址:https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/access-token/auth.getAccessToken.html
     */
  public function getAccessToken()
    {
        $appId = config('wechat.mini_program.default.app_id');# 获取配置文件中的appId
        $secret = config('wechat.mini_program.default.secret');# 获取配置文件中的secret
        $url = "https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid={$appId}&secret={$secret}";

        $key = 'mini_program_access_token';//缓存小程序key
        $expiration_time = 3600;//过期时间

        $accessToken = \Cache::get($key);
        if (!empty($accessToken)) {
            return $accessToken;
        }
        $res = json_decode(file_get_contents($url), true);# 使用http客户端调用
        if (!empty($res['errcode'])) {
            return response()->json(['code' => '5000', 'message' => '请求异常', 'data' => $res]);
        }
        \Cache::set($key, $res['access_token'], $expiration_time); //保存缓存
        return $res['access_token'];
    }
```

**解决测试调用token线上token失效问题**

```shell
# 创建公用路由
 Route::post('get_mini_program_access_token', 'MiniProgram/getDevAccessToken');//解决测试服获取小程序access_token问题
# 对应控制器
 public function getDevAccessToken(Request $request)
    {
        try {
            if (empty(request()->param("appsecret"))) throw new SystemException('密钥不能为空');
            $res = $this->miniProgram->getAccessToken();
            return $this->resSuccess($res, "accessToken返回成功");
        } catch (SystemException $systemException) {
            return $this->resError($systemException->getMessage());
        }
    }
    
  # 对应方法
  class MiniProgramService
{
    protected $accessToken;

    public function __construct()
    {
        $this->appId = config('wechat.wechat_xcx.appid');# 获取配置文件中的appId
        $this->secret = config('wechat.wechat_xcx.appsecret');# 获取配置文件中的secret
        # 一定要放在最后调用
        $this->accessToken = $this->getAccessToken();
    }

    public function getAccessToken()
    {
        if (env('APP_ENV') == 'dev') {  # 如果是测试服的小程序直接调用线上返回
            if (request()->param('appsecret') != $this->secret) throw new SystemException("非法请求");
            $url = "https://api.jiazhengserve.com/api/get_mini_program_access_token";
            $data = ["appsecret" => $this->secret];
            $res = json_decode(request_post($url, $data));
            return $res->data;
        }
        # 正常逻辑
        $url = "https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid={$this->appId}&secret={$this->secret}";
        $key = 'mini_program_access_token';//缓存小程序key
        $expiration_time = 3600;//过期时间

        $accessToken = Cache::get($key);
        if (!empty($accessToken)) {
            return $accessToken;
        }
        $res = json_decode(file_get_contents($url), true);# 使用http客户端调用
        if (!empty($res['errcode'])) {
            Log::info("请求小程序accesstoken:" . json_encode($res));
            throw new SystemException('请求异常');
        }
        Cache::set($key, $res['access_token'], $expiration_time); //保存缓存
        return $res['access_token'];
    }
}
```



## 统一调用请求

```php
/**
 * http请求
 * @param $str
 * @return string
 */
if (!function_exists('http_request')) {
    function http_request($url, $data = null)
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
        curl_close($curl);
        return $output;
    }
}
```

# [小程序码](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/qr-code/wxacode.createQRCode.html)

## [createQRCode](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/qr-code/wxacode.createQRCode.html) 创建小程序二维码(有数量限制)

**代码示例**

```php
 /**
     * @param string $path 扫码进入的小程序页面路径，最大长度 128 字节，不能为空；
     * @param int $width 二维码的宽度，单位 px。最小 280px，最大 1280px 非必传
     */
    public function createQRCode($path, $width = 430)
    {
        if ($path == null) {
            $path = "pages/index/index";
        };# 设置一个默认值
        $access_token = $this->getAccessToken();
        $url = "https://api.weixin.qq.com/cgi-bin/wxaapp/createwxaqrcode?access_token={$access_token}";
        $data = ['path' => $path, 'width' => $width];
        $res = $this->request_by_curl($url, $data);
        # 如果目录不存在创建保存图片目录
        $dir_name = 'image'; //目录名称
        $dir_path = public_path($dir_name);
        if (!is_dir($dir_path)) {
            mkdir($dir_path);
        }
        # 需要保存的位置
        $file_name = $path . 'mini_progra_mqr_code.png';
        file_put_contents("$dir_name/$file_name", $res); # 保存二维码
        if (!file_exists($dir_path . '/' . $file_name)) {
            return response()->json(['code' => '5000', 'message' => '保存失败']);
        }
        return url($dir_name . '/' . $file_name);
    }

    public function request_by_curl($remote_server, $post_string)
    {
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $remote_server);
        curl_setopt($ch, CURLOPT_POST, 1);
        curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, 5);
        curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json;charset=utf-8'));
        curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($post_string));
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        // 线下环境不用开启curl证书验证, 未调通情况可尝试添加该代码
        curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);
        curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, 0);
        $data = curl_exec($ch);
        curl_close($ch);
        return $data;
    }
```

## getUnlimited获取小程序二维码(不限制数量)

```php
    /**
     * 生成小程序码
     * @param array $data
     * @throws SystemException
     */
    public function getQRCode(array $data)
    {
        $path = $data['path'] ?? "";
        $query = $data['query'] ?? "";
        $width = !empty($data['width']) ? $data['width'] : 430;
        if (empty($path)) throw new SystemException("路径不能为空");
        if (empty($query)) throw new SystemException("查询参数不能为空");
        if ($width < 280) throw new SystemException("宽度最小280px");
        if ($width > 1280) throw new SystemException("宽度最大1280px");
        $url = "https://api.weixin.qq.com/wxa/getwxacodeunlimit?access_token={$this->accessToken}";
        $data = [
            'scene' => $query,
            'width' => $width,
            'page' => $path,
            'check_path' => true,
        ];
        $key = md5($query . $width . $path);
        $retData = Cache::get($key);
        if (!empty($retData)) return $retData;
        $res = request_post($url, json_encode($data));
        $image_base64 = base64_encode($res);
        Cache::set($key, $image_base64, 60 * 60 * 2);
        return $image_base64;
    }
# 调用
 public function qrCode(Request $request)
    {
        try {
            $data = $request->only(['path', 'query', 'width']);
            $res = $this->miniProgram->getQRCode($data);
            return $this->resSuccess($res, '小程序二维码返回成功');
        } catch (SystemException $systemException) {
            return $this->resError($systemException->getMessage());
        }
    }
```

**前端如何获取二维码携带的参数内容/场景scene值**

> 如下如箭头所标记的值传递给 前端之后**需要前端自行处理**

- 资料

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/qq_25102811/article/details/110198152?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-110198152-blog-113602872.pc_relevant_multi_platform_whitelistv3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-110198152-blog-113602872.pc_relevant_multi_platform_whitelistv3&utm_relevant_index=1)  [link](https://blog.csdn.net/weixin_42728646/article/details/113602872?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-113602872-blog-106052423.pc_relevant_multi_platform_whitelistv3&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-113602872-blog-106052423.pc_relevant_multi_platform_whitelistv3&utm_relevant_index=1) |

![image-20220812155446400](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220812155446400.png)

# 手机号

## [getPhoneNumber](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/phonenumber/phonenumber.getPhoneNumber.html)

### 请求参数

| 属性                                  | 类型   | 默认值 | 必填 | 说明                                                         |
| :------------------------------------ | :----- | :----- | :--- | :----------------------------------------------------------- |
| access_token / cloudbase_access_token | string |        | 是   | [接口调用凭证](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/access-token/auth.getAccessToken.html) |
| code                                  | string |        | 是   | [手机号获取凭证](https://developers.weixin.qq.com/miniprogram/dev/framework/open-ability/getPhoneNumber.html) |

**代码示例**

> 这里注意前端传输过来的code，是这个https://developers.weixin.qq.com/miniprogram/dev/framework/open-ability/getPhoneNumber.html
>
> 参考：https://developers.weixin.qq.com/community/develop/doc/0004e6249ecc4881983d32eec55c00
>
> ```php
> 不要用 wx.login 获取到的 code，是错的。你应该要用
>  <button open-type="getPhoneNumber" bindgetphonenumber="getPhoneNumber">getPhoneNumber</button>
> bindgetphonenumber 回调中 e.detail.code
> 具体参考：https://developers.weixin.qq.com/miniprogram/dev/framework/open-ability/getPhoneNumber.html
> ```

```php
public function getMiniAccessToken()
    {
        $appId = config('collocation.xcx_appId');# 获取配置文件中的appId
        $secret = config('collocation.xcx_appSecret');# 获取配置文件中的secret
        $url = "https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid={$appId}&secret={$secret}";
        $key = 'mini_program_access_token';//缓存小程序key
        $expiration_time = 3600;//过期时间
        $accessToken = \Cache::get($key);
        if (!empty($accessToken)) {
            return $accessToken;
        }
        $res = json_decode(file_get_contents($url), true);# 使用http客户端调用
        if (!empty($res['errcode'])) {
            return response()->json(['code' => '5000', 'message' => '请求异常', 'data' => $res]);
        }
        \Cache::put($key, $res['access_token'], $expiration_time); //保存缓存
        return $res['access_token'];
    }
    public function test()
    {
      $token=$this->getMiniAccessToken();
     // dd($token);
      $code="033m5mml2KG8p84noIol2nldND0m5mmA";
      $url="https://api.weixin.qq.com/wxa/business/getuserphonenumber?access_token={$token}";
      $data=['code'=>$code];
      $res=$this->request_by_curl($url,$data);
      dd($res);
    }
    public function request_by_curl($remote_server, $post_string)
    {
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $remote_server);
        curl_setopt($ch, CURLOPT_POST, 1);
        curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, 5);
        curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json;charset=utf-8'));
        curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($post_string));
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        // 线下环境不用开启curl证书验证, 未调通情况可尝试添加该代码
        curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);
        curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, 0);
        $data = curl_exec($ch);
        curl_close($ch);
        return $data;
    }
```

# [URL Scheme](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/url-scheme/urlscheme.generate.html)

## [generate](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/url-scheme/urlscheme.generate.html)(打开小程序的跳转链接)

> [**作用**](https://zhuanlan.zhihu.com/p/342447284)
>
> > 可以用于给前端H5跳转小程序(scheme码是啥，可以理解为一个打开小程序的跳转链接)
>
> 获取小程序 scheme 码，适用于短信、邮件、外部网页、微信内等拉起小程序的业务场景。目前仅针对国内非个人主体的小程序开放，详见[获取 URL scheme](https://developers.weixin.qq.com/miniprogram/dev/framework/open-ability/url-scheme.html)。

**效果示例**

<img src="https://s2.loli.net/2022/04/28/VxAsHUEIJ5OD3wb.gif" alt="Screenshot_2022_0428_085826.gif" style="zoom:50%;" />

**页面参数查询**

> **注意这里页面参数可以图片中示例中去取**,<font color="red">注意测试的只能跳线上小程序如果携带参数请携带线上参数</font>

![](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220721143908645.png)

**代码示例**

```php
   # 控制器
    public function schemeCode(Request $request)
    {
        try {
            $data=$request->only(['path','query']);
            if (empty($data['path']))throw new SystemException('小程序页面路径不能为空');
            $res = $this->miniProgram->getSchemeCode($data);
            return $this->resSuccess($res, '小程序scheme码返回成功');
        } catch (SystemException $systemException) {
            return $this->resError($systemException->getMessage());
        }

    }

   /**
     *获取小程序 scheme 码
     */
    public function getSchemeCode(array $data)
    {
        $path = $data['path'] ?? "";
        $query = $data['query'] ?? ""; //携带参数
        $accessToken = $this->getAccessToken();
        $url = "https://api.weixin.qq.com/wxa/generatescheme?access_token={$accessToken}";
        $postData = [
            'jump_wxa' => [
                'path' => $path,//跳转到的目标小程序信息
                'query' => $query,
                'env_version' => '',
            ],
            'expire_type' => 0,//到期失效的 scheme 码失效类型，失效时间：0，失效间隔天数：1
            'expire_time' => strtotime("+11 month"),//到期失效的 scheme 码失效类型，失效时间：0，失效间隔天数：1
        ];
        $res = request_post($url, json_encode($postData));
        $res = json_decode($res, true);
        if (!empty($res) && $res['errcode'] == 0 && $res['errmsg'] == 'ok') {
            return $res['openlink'];
        }
        throw new SystemException($res);
    }
```

**返回示例**

```php
{
    "code": 200,
    "msg": "小程序scheme码返回成功",
    "data": "weixin://dl/business/?t=BZB2WNmiqqb"
}
```

## bug解析

### **"errcode" => 85401 "errmsg" => "time limit between 1min and 1year rid: xxxxxxxxxxx**

> 由报错原因我们可以看出**时间限制在1分钟到1年之间** 所以如果**expireType==0**的情况下**expireTime 设置为Unix时间戳格式(全是数字的那种格式)** 
>
> 转化出来的时间必须大于当前时间1分钟或者一年之间  推荐修改为   **'expire_time' => strtotime("+11 month")**

![image-20220528111412584](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220528111412584.png)



###  携带参数问题

> <font color='red'> 注意测试的只能跳线上小程序如果携带参数请携带线上参数</font>
>
> 参数使用**query**  传递字符 例子**id=940**

![image-20220721144301942](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220721144301942.png)





