##  介绍

> 新入职了一个公司 有一个功能 是 登录功能对接**line**

## uni-app LINE 登录全方案（分 APP/H5 / 小程序三端）

LINE 基于**OAuth2.1+OIDC**，uni 官方无内置 LINE 登录，**APP 原生插件、H5 网页 OAuth、小程序 webview 跳转**3 种实现方式，**必须后端承接 token、不能前端直拿 secret**。

### 一、前置：LINE 开发者后台配置（所有端通用）

####  1、注册创建 Channel

1. 登录：[https://developers.line.biz/console/](https://link.wtturl.cn/?target=https%3A%2F%2Fdevelopers.line.biz%2Fconsole%2F&scene=im&aid=497858&lang=zh)（境外网络）

#### 2. 新建 **LINE Login Channel**，勾选：`Web app + iOS + Android`（多端共用一个 Channel）

#### 3.保存关键参数：

- `Channel ID`、`Channel Secret`（密钥，**只放后端，前端禁止保存**）

#### 4. **LINE Login 配置页必填**：

- ✅ Callback URL（回调地址）：H5 填线上域名、APP 不用填网页回调；多地址换行填写
- ✅ Android：填写 APP 包名（如`com.xxx.app`）
- ✅ iOS：填写 Bundle ID（如`com.xxx.app`）
- ✅ 开启：`Use LINE Login in mobile app`（APP 端必开）

## api文档

### [2.1](https://developers.line.biz/en/reference/line-login/)

请求参数如下：

| 参数          | 必填 | 说明                                            |
| ------------- | ---- | ----------------------------------------------- |
| grant_type    | 是   | 授权类型，授权码模式固定为 `authorization_code` |
| code          | 是   | LINE 授权成功后回调返回的授权码                 |
| redirect_uri  | 是   | 必须与开发者后台配置的一致                      |
| client_id     | 是   | LINE Channel ID                                 |
| client_secret | 是   | LINE Channel Secret                             |

### token获取后 请求用户资料

https://developers.line.biz/en/docs/line-login/managing-users/

### service封装

```bash
<?php
namespace App\Services;

class LineAuthService
{
    private string $clientId;
    private string $clientSecret;
    private string $redirectUri;
    private string $tokenUrl = "https://api.line.me/oauth2/v2.1/token";

    public function __construct(string $clientId, string $clientSecret, string $redirectUri)
    {
        $this->clientId = $clientId;
        $this->clientSecret = $clientSecret;
        $this->redirectUri = $redirectUri;
    }

    /**
     * 用 authorization_code 换取 access_token
     * @param string $code 授权码
     * @return array
     */
    public function getAccessToken(string $code): array
    {
        $data = [
            'grant_type' => 'authorization_code',
            'code' => $code,
            'redirect_uri' => $this->redirectUri,
            'client_id' => $this->clientId,
            'client_secret' => $this->clientSecret
        ];

        return $this->requestToken($data);
    }

    /**
     * 用 refresh_token 刷新 access_token
     * @param string $refreshToken
     * @return array
     */
    public function refreshAccessToken(string $refreshToken): array
    {
        $data = [
            'grant_type' => 'refresh_token',
            'refresh_token' => $refreshToken,
            'client_id' => $this->clientId,
            'client_secret' => $this->clientSecret
        ];

        return $this->requestToken($data);
    }

    /**
     * PKCE 模式下用 code 换 token（无需 client_secret）
     * @param string $code
     * @param string $codeVerifier
     * @return array
     */
    public function getAccessTokenByPKCE(string $code, string $codeVerifier): array
    {
        $data = [
            'grant_type' => 'authorization_code',
            'code' => $code,
            'redirect_uri' => $this->redirectUri,
            'client_id' => $this->clientId,
            'code_verifier' => $codeVerifier
        ];

        return $this->requestToken($data);
    }

    /**
     * 统一请求 token 接口
     * @param array $postData
     * @return array
     */
    private function requestToken(array $postData): array
    {
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $this->tokenUrl);
        curl_setopt($ch, CURLOPT_POST, true);
        curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query($postData));
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($ch, CURLOPT_HTTPHEADER, [
            'Content-Type: application/x-www-form-urlencoded'
        ]);

        $response = curl_exec($ch);
        $err = curl_error($ch);
        curl_close($ch);

        if ($err) {
            return [
                'error' => true,
                'message' => $err
            ];
        }

        return json_decode($response, true);
    }
}
```

**使用示例**

```php
use App\Services\LineAuthService;

// 初始化
$lineService = new LineAuthService(
    '你的ChannelID',
    '你的ChannelSecret',
    'https://your-domain.com/line/callback'
);

// 1. 用 code 换 token
$tokenData = $lineService->getAccessToken($_GET['code']);
print_r($tokenData);

// 2. 用 refresh_token 刷新
$tokenData = $lineService->refreshAccessToken($refreshToken);
print_r($tokenData);

// 3. PKCE 模式
$tokenData = $lineService->getAccessTokenByPKCE($code, $codeVerifier);
print_r($tokenData);
```

