##  流程说明

> D:\phpstudy_pro\WWW\vibetv\application\common\controller\Api.php   `_initialize`
>
> D:\phpstudy_pro\WWW\vibetv\application\common\library\Auth.php   `init`

会默认调用这里的两个方法去`user_token`  中比较用户的token



##  接口中获取用户信息

```php
//    protected $noNeedLogin = ['*'];
//把api中的$noNeedLogin先注销走鉴权

$this->auth->id  //接口中调用可以获取用户id
```

## postman传递token

例如我想请求`application\api\controller\video\Comment.php `   下的 `add()`

`video` 是文件名

`Comment` 是控制器

```php
<?php

namespace app\api\controller\video;

use app\common\controller\Api;
use think\Request;

class Comment extends Api
{

    protected $noNeedLogin = [];
    protected $noNeedRight = '*';
    public function add(Request $request)
    {
         halt(111);
    }
}
```

postman的`headers`中添加`token`即可

## 封装统一获取用户id `trait类`

> 为啥要封装**如果不封装代码无法解耦**  因为我需要在**service**中获取用户登录信息 总不能所有代码全写在控制器中吧

### 封装traits

```php
<?php

namespace app\common\traits;

/**
 * 用户信息 Trait（兼容 FastAdmin 官方 auth）
 */
trait UserInitTrait
{
    /**
     * 当前登录用户ID
     * @var int
     */
    protected $userId = 0;

    /**
     * 当前登录用户完整信息
     * @var array
     */
    protected $userInfo = [];

    /**
     * 初始化用户信息（正确获取 auth 方式）
     */
    protected function initUserInfo()
    {
        // ✅ 正确获取 FastAdmin 登录鉴权对象
        $auth = \app\common\library\Auth::instance();
        
        // 赋值用户ID
        $this->userId = $auth->id;
        
        // 赋值用户完整信息
        $this->userInfo = $auth->user ?: [];
    }
}
```

### 调用

```php
<?php
namespace app\admin\controller\demo;

use app\common\controller\Backend;
use app\common\traits\UserInitTrait;

class Demo extends Backend
{
    // 引入 trait
    use UserInitTrait;

    public function _initialize()
    {
        parent::_initialize();
        
        // 调用初始化
        $this->initUserInfo();
    }

    public function index()
    {
        // 直接用！
        echo $this->userId;      // 用户ID
        echo $this->userInfo['username']; // 用户名
    }
}
```



