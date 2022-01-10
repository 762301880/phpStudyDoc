#   资料

| 名称           | 地址                                       |
| -------------- | ------------------------------------------ |
| easywechat官网 | [链接](https://easywechat.vercel.app/6.x/) |
|                |                                            |





# [微信支付](https://easywechat.vercel.app/4.x/payment/)

**参考资料**

| 名称       | 地址                                                |
| ---------- | --------------------------------------------------- |
| 第三方博客 | [link](https://segmentfault.com/a/1190000016177743) |
|            |                                                     |

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
    # 路径需要填写服务器或者本地保存的绝对路径 例如 /data/work/laravel/public/cert/apiclient_cert.pem
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



## [扫码支付](https://easywechat.vercel.app/4.x/payment/scan-pay.html#%E6%89%AB%E7%A0%81%E6%94%AF%E4%BB%98)

### 先下单，生成订单后创建二维码

> [文本生成二维码在线](https://cli.im/text)

```php
        $result = $this->app->order->unify([
            'body' => '腾讯充值中心-QQ会员充值',
            'out_trade_no' => '20150806125346asda',
            'total_fee' =>1,
            //'spbill_create_ip' => '123.12.12.123', // 可选，如不传该参数，SDK 将会自动获取相应 IP 地址
           'notify_url' => 'http://42f4a70e.cpolar.io/api/notice', // 支付结果通知网址，如果不设置则会使用配置里的默认地址(换成自己的通知回调地址)
            'trade_type' => 'NATIVE',
            'product_id' =>789, // $message['product_id'] 则为生成二维码时的产品 ID(通俗的来说就是商品id)
            'openid' => 'olKLd5Z8hu6gyTiOZ5jHHgUpmWis', //用户自己的openid
        ]);
        dd($result);
# 返回示例  
array:10 [
  "return_code" => "SUCCESS"
  "return_msg" => "OK"
  "result_code" => "SUCCESS"
  "mch_id" => "1601524050"
  "appid" => "wxbc238451ad42add0"
  "nonce_str" => "9TGxQYcWykrNwtCu"
  "sign" => "36A36827923690F9F6680590C1C13203"
  "prepay_id" => "wx1013402863376836d1c563469d01040000"
  "trade_type" => "NATIVE"
  "code_url" => "weixin://wxpay/bizpayurl?pr=MOw2JtFzz"
]
# 直接用code_url生成二维码即可    
```

**支付成功回调通知**

**测试1不推荐使用**

> 测试一下传递的回调参数

```shell
   use EasyWeChat\Kernel\Support\XML;
   public function notice(Request $request)
    {
        $res=XML::parse($request->getContent());
        \Log::channel('wechat_pay_notice')->info($res);
        
        #$data = file_get_contents(‘php://input’); 可以将获取的数据写入日志中查看。
     }
 # 返回    
[2022-01-10 14:39:48] local.INFO: array (
  'appid' => 'wxbc238451ad42add0',
  'bank_type' => 'OTHERS',
  'cash_fee' => '1',
  'fee_type' => 'CNY',
  'is_subscribe' => 'N',
  'mch_id' => '1601524050',
  'nonce_str' => '61dbd495517b5',
  'openid' => 'olKLd5Z8hu6gyTiOZ5jHHgUpmWis',
  'out_trade_no' => '2015080612210',
  'result_code' => 'SUCCESS',
  'return_code' => 'SUCCESS',
  'sign' => '8DAC3AF8E36ED9EBCD10491A168B0863',
  'time_end' => '20220110143942',
  'total_fee' => '1',
  'trade_type' => 'NATIVE',
  'transaction_id' => '4200001320202201103302411424',
)  
```

[**推荐使用测试通知**](https://easywechat.vercel.app/4.x/payment/notify.html#%E9%80%80%E6%AC%BE%E7%BB%93%E6%9E%9C%E9%80%9A%E7%9F%A5)

```php
        $response = $this->app->handleScannedNotify(function ($message, $fail, $alert){
            // \Log::channel('wechat_pay_notice')->info('进不来啊进不来啊');
           \Log::channel('wechat_pay_notice')->warning($message);
            // \Log::channel('wechat_pay_notice')->error($fail);
            // \Log::channel('wechat_pay_notice')->error($alert);
            // 你的逻辑
            //return true;
            // 或者错误消息
        });
        $res=$response->send();
        \Log::channel('wechat_pay_notice')->alert('测试通知');

# 返回示例
[2022-01-10 15:09:36] local.WARNING: array (
  'appid' => 'wxbc238451ad42add0',
  'bank_type' => 'OTHERS',
  'cash_fee' => '1',
  'fee_type' => 'CNY',
  'is_subscribe' => 'N',
  'mch_id' => '1601524050',
  'nonce_str' => '61dbcc907d295',
  'openid' => 'olKLd5Z8hu6gyTiOZ5jHHgUpmWis',
  'out_trade_no' => '201508061225',
  'result_code' => 'SUCCESS',
  'return_code' => 'SUCCESS',
  'sign' => '3436B4C83FE9E697239DD7E5A9E1516D',
  'time_end' => '20220110140526',
  'total_fee' => '1',
  'trade_type' => 'NATIVE',
  'transaction_id' => '4200001340202201105772953255',
)  
[2022-01-10 15:09:36] local.ALERT: 测试通知  
```

