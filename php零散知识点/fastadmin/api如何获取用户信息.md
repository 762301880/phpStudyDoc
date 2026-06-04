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

