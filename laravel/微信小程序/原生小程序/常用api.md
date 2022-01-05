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
        \Cache::set($key, $res['access_token'], $expiration_time); //保存缓存
        return $res['access_token'];
    }
```



