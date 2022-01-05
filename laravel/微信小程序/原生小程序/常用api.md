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

