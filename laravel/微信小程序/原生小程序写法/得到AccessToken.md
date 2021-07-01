#  一、讲解说明

 [官网文档说明地址](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/access-token/auth.getAccessToken.html)

- 什么是access_token

> 获取小程序全局唯一后台接口调用凭据（`access_token`）。**调用绝大多数后台接口时都需使用 access_token，开发者需要进行妥善保存。**
>
> 简单来说就是调用微信提供的后端`api`接口,大多数都需要`access_token`

## 1.1请求地址

 ```apl
 GET https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=APPID&secret=APPSECRET
 ```

## 1.2 请求参数

| 属性       | 类型   | 默认值 | 必填 | 说明                                                         |
| :--------- | :----- | :----- | :--- | :----------------------------------------------------------- |
| grant_type | string |        | 是   | 填写 client_credential                                       |
| appid      | string |        | 是   | 小程序唯一凭证，即 AppID，可在「[微信公众平台](https://mp.weixin.qq.com/) - 设置 - 开发设置」页中获得。（需要已经成为开发者，且帐号没有异常状态） |
| secret     | string |        | 是   | 小程序唯一凭证密钥，即 AppSecret，获取方式同 appid           |

## 1.3 返回值

### 返回值

### Object

返回的 JSON 数据包

| 属性         | 类型   | 说明                                           |
| :----------- | :----- | :--------------------------------------------- |
| access_token | string | 获取到的凭证                                   |
| expires_in   | number | 凭证有效时间，单位：秒。目前是7200秒之内的值。 |
| errcode      | number | 错误码                                         |
| errmsg       | string | 错误信息                                       |

## 1.4**errcode 的合法值**

| 值    | 说明                                                         | 最低版本 |
| :---- | :----------------------------------------------------------- | :------- |
| -1    | 系统繁忙，此时请开发者稍候再试                               |          |
| 0     | 请求成功                                                     |          |
| 40001 | AppSecret 错误或者 AppSecret 不属于这个小程序，请开发者确认 AppSecret 的正确性 |          |
| 40002 | 请确保 grant_type 字段值为 client_credential                 |          |
| 40013 | 不合法的 AppID，请开发者检查 AppID 的正确性，避免异常字符，注意大小写 |          |

## 1.5 返回数据示例

正常返回

```json
{"access_token":"ACCESS_TOKEN","expires_in":7200}
```

错误时返回

```json
{"errcode":40013,"errmsg":"invalid appid"}
```

## 1.6  access_token 的存储与更新

- `access_token` 的存储至少要保留 512 个字符空间；
- `access_token` 的有效期目前为 **2 个小时**，需定时刷新，重复获取将导致上次获取的 `access_token` 失效；
- 建议开发者使用中控服务器统一获取和刷新 `access_token`，其他业务逻辑服务器所使用的 `access_token` 均来自于该中控服务器，不应该各自去刷新，否则容易造成冲突，导致 `access_token` 覆盖而影响业务；
- `access_token` 的有效期通过返回的 `expires_in` 来传达，目前是7200秒之内的值，中控服务器需要根据这个有效时间提前去刷新。在刷新过程中，中控服务器可对外继续输出的老 `access_token`，此时公众平台后台会保证在5分钟内，新老 `access_token` 都可用，这保证了第三方业务的平滑过渡；
- `access_token` 的有效时间可能会在未来有调整，所以中控服务器不仅需要内部定时主动刷新，还需要提供被动刷新 `access_token` 的接口，这样便于业务服务器在API调用获知 `access_token` 已超时的情况下，可以触发 `access_token` 的刷新流程。

> 详情可参考微信公众平台文档 [《获取access_token》](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421140183)

## 在线调试

开发者可以使用[网页调试工具](https://mp.weixin.qq.com/debug/cgi-bin/apiinfo?t=index&type=基础支持&form=获取access_token接口 /token&token=&lang=zh_CN)调试该接口

#  二、laravel中获取access_token接口示例

> 如何获取`app_id`&`secret`就不细说了请自行百度

## 2.1路由

```php
Route::any('get_access_token', [MiniWechatController::class, 'getAccessToken']);#得到AccessToken
```

## 2.2 控制器

```php
    /*
     * 返回AccessToken
     * 文档地址:https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/access-token/auth.getAccessToken.html
     */
    public function getAccessToken()
    {
        $appId = Config::get('wechat.mini_program.default.app_id');# 获取配置文件中的appId
        $secret = Config::get('wechat.mini_program.default.secret');# 获取配置文件中的secret
        $url = "https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid={$appId}&secret={$secret}";
        $data = Http::get($url)->json();# 使用http客户端调用
        return $data['access_token'];#返回请求的数据
    }
```



