#   资料

| 名称           | 地址                                       |
| -------------- | ------------------------------------------ |
| easywechat官网 | [链接](https://easywechat.vercel.app/6.x/) |
|                |                                            |





# 微信支付

**配置支付参数**

```php
use EasyWeChat\Factory;
# 最好做成这种全局的调用省的每次都要实例化一下配置
public function __construct()
{
 $config = [
    // 必要配置
    'app_id'             => 'xxxx',#(小程序的appid)
    'mch_id'             => 'your-mch-id',#(支付平台提供)
    'key'                => 'key-for-signature',   // API 密钥(支付平台提供)

    // 如需使用敏感接口（如退款、发送红包等）需要配置 API 证书路径(登录商户平台下载 API 证书)
    'cert_path'          => 'path/to/your/cert.pem', // XXX: 绝对路径！！！！(支付平台提供)
    'key_path'           => 'path/to/your/key',      // XXX: 绝对路径！！！！(支付平台提供)
    'notify_url'         => '默认的订单回调地址',     // 你也可以在下单时单独设置来想覆盖它(支付成功或者失败回调地址)
      //'sandbox' => true, 开启沙箱支付(设置为 false 或注释则关闭沙箱模式)
];
$this->app = Factory::payment($config);
// 判断当前是否为沙箱模式：
//bool $app->inSandbox();
 }
```

**调用支付**

> ## 统一下单H5 支付，公众号支付，扫码支付，支付中签约，全部都是用这个接口下单

```php
        //$bool=$app->inSandbox(); //返回判断是否是沙箱支付
        $orderInfo=[
            'body' => '腾讯充值中心-QQ会员充值',
            'out_trade_no' => '20150806125346asd',
            'total_fee' => 1,
            //'spbill_create_ip' => '123.12.12.123', // 可选，如不传该参数，SDK 将会自动获取相应 IP 地址
            'notify_url' => 'https://pay.weixin.qq.com/wxpay/pay.action', // 支付结果通知网址，如果不设置则会使用配置里的默认地址
            'trade_type' => 'JSAPI', // 请对应换成你的支付方式对应的值类型
            'openid' => 'olKLd5blEKZ5PzqdDi9aoSbdJiYI', //用户自己的openid
        ];
        $result = $app->order->unify($orderInfo);
# 返回示例
// array:9 [
//   "return_code" => "SUCCESS"
//   "return_msg" => "OK"
//   "result_code" => "SUCCESS"
//   "mch_id" => "1601524050"
//   "appid" => "wxbc238451ad42add0"
//   "nonce_str" => "LUJ7JcX21UlgcuBh"
//   "sign" => "76DCA7EEA013CC0FDAC012426302C45F"
//   "prepay_id" => "wx10095416710776f540e6a978ba64660000"
//   "trade_type" => "JSAPI"
// ]
```

## 查询订单

### 根据商户订单号查询

```php
#订单号 调用支付时候传递的out_trade_no(自定义的订单编号)
$this->app->order->queryByOutTradeNumber("商户系统内部的订单号（out_trade_no）");

# 返回示例
//  array:12 [
//   "return_code" => "SUCCESS"
//   "return_msg" => "OK"
//   "result_code" => "SUCCESS"
//   "mch_id" => "1601524050"
//   "appid" => "wxbc238451ad42add0"
//   "device_info" => null
//   "trade_state" => "NOTPAY"
//   "total_fee" => "1"
//   "out_trade_no" => "20150806125346asd"
//   "trade_state_desc" => "订单未支付"
//   "nonce_str" => "8dZJdXOW9V8Wffqj"
//   "sign" => "BD09412A53EE1723D7C8EBCC0F1D676F"
// ]
```

### 根据微信订单号查询

```shell
$app->order->queryByTransactionId("微信订单号（transaction_id）");
```

### 关闭订单

> 注意：订单生成后不能马上调用关单接口，最短调用时间间隔为 5 分钟。

```php
$app->order->close(商户系统内部的订单号（out_trade_no）);
```

