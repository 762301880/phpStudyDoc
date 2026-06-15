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

## 登录流程

### 一、前置配置（LINE 开发者后台必做）

1. 登录 [developers.line.biz](https://link.wtturl.cn/?target=https%3A%2F%2Fdevelopers.line.biz&scene=im&aid=497858&lang=zh) → 创建 Provider→新建**LINE Login 通道**
2. 拿到关键参数写入项目`.env`（你已经在用）

```php
[line]
line_client_id=ChannelID
line_client_secret=ChannelSecret
line_redirect_uri=https://你的域名/api/line/get_access_token  #必须可外网访问
```

> **关键：LINE 后台【LINE Login】→Redirect settings 填写上面回调地址，地址必须完全一致（域名、路径、http/https），多地址换行填写**LINE Developers

1. 需要邮箱：后台 OpenID Connect 提交邮箱权限申请，审核通过才能拉取 email，`scope`带上`email`

### 二、步骤 1：前端发起 LINE 授权跳转（H5/APP 前端操作）

**授权地址：`https://access.line.me/oauth2/v2.1/authorize`**

拼接参数 GET 跳转，**必传参数**：

|     参数      |                             说明                             |
| :-----------: | :----------------------------------------------------------: |
| response_type |                   固定`code`（授权码模式）                   |
|   client_id   |                          ChannelID                           |
| redirect_uri  |                  和后台配置回调地址一模一样                  |
|     state     |         随机字符串（防 CSRF，登录前后端存一份对比）          |
|     scope     | `profile openid email`（profile = 昵称头像；openid 必带；email 需后台申请权限）LINE Developers |

前端示例跳转：

```js
let params = {
  response_type:"code",
  client_id:"xxx",
  redirect_uri:"https://域名/api/line/get_access_token",
  state:随机串,
  scope:"profile openid email"
}
location.href = "https://access.line.me/oauth2/v2.1/authorize?"+new URLSearchParams(params)
```

### 三、步骤 2：用户授权，LINE 自动回调你的接口（你的`/api/line/get_access_token`）

1. 用户在 LINE 页面点【同意授权】，LINE 携带`code=xxx&state=xxx`**GET 请求回调地址**（就是你写的接口地址）
2. 你的`getAccessToken()`接收`code`，**第一步校验 state（前端传的和后端缓存一致，防止伪造）**

> 你当前接口：`/api/line/get_access_token` 就是回调接收地址

### 四、步骤 3：后端用 code 换取 access_token（你 LineAuthService 核心方法）

**接口：POST [https://api.line.me/oauth2/v2.1/token](https://link.wtturl.cn/?target=https%3A%2F%2Fapi.line.me%2Foauth2%2Fv2.1%2Ftoken&scene=im&aid=497858&lang=zh)**，表单传参，不可 GET

```php
//LineAuthService getAccessToken方法
public function getAccessToken($code,$state=''){
    //1.state校验
    //2.请求token接口
    $post = [
        'grant_type'=>'authorization_code',
        'code'=>$code,
        'client_id'=>$this->clientId,
        'client_secret'=>$this->clientSecret,
        'redirect_uri'=>$this->redirectUri
    ];
    $res = http_post('https://api.line.me/oauth2/v2.1/token',$post);
    //返回字段：access_token、refresh_token、id_token、expires_in
}
```

返回数据：

```json
{
  "access_token":"xxx",
  "refresh_token":"xxx",
  "id_token":"jwt字符串",
  "expires_in":2592000,
  "token_type":"Bearer"
}
```

### 五、步骤 4：access_token 获取 LINE 用户信息

**用户信息接口：GET [https://api.line.me/oauth2/v2.1/userinfo](https://link.wtturl.cn/?target=https%3A%2F%2Fapi.line.me%2Foauth2%2Fv2.1%2Fuserinfo&scene=im&aid=497858&lang=zh)，Header：Authorization: Bearer {access_token}**

返回用户原生数据：

```json
{
  "sub":"LINE唯一openid",
  "name":"昵称",
  "picture":"头像url",
  "email":"邮箱（有权限才返回）"
}
```

> `sub`= 用户唯一 ID，作为我方系统第三方唯一标识

### 六、步骤 5：FastAdmin 本地登录逻辑（对接会员表 fa_user）

拿到 openid、昵称、头像、邮箱后：

1. 查询

   ```
   fa_user
   ```

   或第三方绑定表：

   是否已有该 LINE openid 绑定用户

   - 已绑定：直接生成我方 Api 登录 token（FastAdmin Api 自带 token 登录）返回前端
   - 未绑定：自动注册新用户入库，绑定第三方关系，生成登录 token

2. 最终接口返回：`{code:1,msg:'登录成功',data:{token:'我方登录令牌',userinfo:{}}}`

### 七、拓展：refresh_token 续期 access_token

access_token 过期用`refresh_token`换新 token，POST 同 token 接口：

```
grant_type=refresh_token&refresh_token=xxx&client_id=xxx&client_secret=xxx
```

### 八、你现有代码改造优化

1. `Line`控制器类名首字母大写`class Line extends Api`
2. `LineAuthService`中`Env::get()`读取配置（已修复 env 报错）
3. `getAccessToken`内部新增：state 校验→换 token→拉用户信息→会员登录逻辑

### 最终接口调用链路

前端跳转 LINE 授权 → LINE 回调`域名/api/line/get_access_token?code=xxx&state=xxx` → 后端换 token→拿用户资料→登录→返回业务 token



## bug解析

## 很垃圾 邮箱注册后 退出后 账号密码什么的全对就是无法登录 

> 具体原因可以网上查 很垃圾的体验感