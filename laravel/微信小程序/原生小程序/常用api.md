#  [接口调用凭证](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/access-token/auth.getAccessToken.html)

## [getAccessToken](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/access-token/auth.getAccessToken.html)

**代码示例**

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



# [小程序码](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/qr-code/wxacode.createQRCode.html)

## [createQRCode](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/qr-code/wxacode.createQRCode.html) 创建小程序二维码

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













