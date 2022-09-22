# [微信支付](https://easywechat.vercel.app/4.x/payment/)

**介绍**

> **1扫码支付**
>
> 根据商品信息生成二维码-> 贴到某个位置->用户扫码->wechat(到达微信)->notyfy(通知你会携带openid+商品信息)
>
> ->下单+微信创建订单->返回给微信(prepay_id)->付款
>
> **2其他支付**
>
> 网站下单->创建微信订单->微信prepay_id->呼起支付(JSSDK,APP)->付款->notify

**参考资料**

| 名称             | 地址                                                         |
| ---------------- | ------------------------------------------------------------ |
| easywechat官网   | [链接](https://easywechat.vercel.app/6.x/)                   |
| 第三方博客       | [link](https://segmentfault.com/a/1190000016177743) [link](https://learnku.com/articles/8613/wechat-small-programs-pay-api-configuration-under-laravel) |
| 微信支付官方文档 | [link](https://pay.weixin.qq.com/static/product/product_intro.shtml?name=jsapi) |

## **支付参数配置介绍**

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
      //'sandbox' => true, 开启沙箱支付(设置为 false 或注释则关闭沙箱模式) 沙箱支付请设置金额为101
];
$this->app = Factory::payment($config); 
// 判断当前是否为沙箱模式：
//bool $app->inSandbox();
 }
```

## **调用支付**

> ## 统一下单H5 支付，公众号支付，扫码支付，支付中签约，全部都是用这个接口下单

```php
        //$bool=$app->inSandbox(); //返回判断是否是沙箱支付
        $orderInfo=[
            'body' => '腾讯充值中心-QQ会员充值',
            'out_trade_no' => '20150806125346asd',
            'total_fee' => 1 * 100, # 微信的 价格都是以分做单位所以价格要乘100
            //'spbill_create_ip' => '123.12.12.123', // 可选，如不传该参数，SDK 将会自动获取相应 IP 地址
            'notify_url' => 'https://pay.weixin.qq.com/wxpay/pay.action', // 支付结果通知网址，如果不设置则会使用配置里的默认地址
            'trade_type' => 'JSAPI', // 请对应换成你的支付方式对应的值类型
            'openid' => 'olKLd5blEKZ5PzqdDi9aoSbdJiYI', //用户自己的openid-小程序的填小程序,公众号的填公众号
        ];
        $result = $app->order->unify($orderInfo); # 调起支付
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

**实例**

```php
 public function payment(array $data)
    {
        $bool = false;
        $body = !empty($data['body']) ? $data['body'] : "";//订单标题
        $order_number = !empty($data['order_number']) ? $data['order_number'] : "";//订单编号
        $payment_price = !empty($data['payment_price']) ? $data['payment_price'] : "";//价格
        $openId = !empty($data['openId']) ? $data['openId'] : "";//用户openid
        # 设置支付信息
        $result = $this->app->order->unify([
            'body' => $body,
            'out_trade_no' => '45655555555555555555555',
            'total_fee' => $payment_price * 100,
            //'spbill_create_ip' => '123.12.12.123', // 可选，如不传该参数，SDK 将会自动获取相应 IP 地址
            'notify_url' => request()->domain() . '/api/order_payment_notice_callback', // 支付结果通知网址，如果不设置则会使用配置里的默认地址(换成自己的通知回调地址)
            'trade_type' => 'JSAPI',
            'openid' => $openId, //用户自己的openid
        ]);
        if ($result['return_code'] == "SUCCESS" && $result['result_code'] == 'FAIL') { # 输出报错原因
            $res = $result['err_code_des'];
            Log::error('调用支付报错:' . '订单编号:' . $order_number . '错误原因:' . json_encode($res));
            return compact('bool', 'res');
        }
        //成功后调用
        $bool = true;
        $res = [ //封装js返回给前端
            'appId' => $this->config['app_id'],
            'timeStamp' => time(),
            'nonceStr' => $result['nonce_str'],
            'package' => 'prepay_id=' . $result['prepay_id'],
            'signType' => 'MD5',
        ];
        $res['paySign'] = generate_sign($res, $this->config['key']);
        //dd((new \EasyWeChat\Payment\Jssdk\Client($this->app))->appConfig( $result['prepay_id']));  # 这一步等于上面的js封装
        return compact('bool', 'res');
    }
```

## 查询订单

### 根据商户订单号查询

```php
#订单号 调用支付时候传递的out_trade_no(自定义的订单编号:随机生成的订单编号)
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

**资料**

| 名称               | 地址                        |
| ------------------ | --------------------------- |
| 文本生成二维码在线 | [link](https://cli.im/text) |

**代码示例**

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

## 公众号支付

**资料**

| 名称                    | 地址                                                         |
| ----------------------- | ------------------------------------------------------------ |
| 微信官方文档-接入前准备 | [link](https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=7_3) |

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

## [处理支付结果通知](https://www.easywechat.com/4.x/payment/notify.html)

> 我们创建订单->提交到微信服务器
>
> notify_url (通知/回调地址) 告诉微信服务器,订单的状态编号,请通过这个地址告诉我
>
> http://domain.com/notify/wechat/order 没有会话状态的,也就是说没有 session cookie(因为这是通过微信服务器请求过来的不是通过用户的手机浏览器请求过来的)
>
> A商户(10018)  http://domain.com/notify/wechat/order/10018   （注意传惨不可以 ?shanghuid=10018,形式传递）
>
> **注意**
>
> 不同的通知请注册不同的通知地址(例如退款是一个,支付成功是一个)

**资料**

| 名称                          | 地址                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| easywechat-通知               | [link](https://www.easywechat.com/4.x/payment/notify.html#%E6%94%AF%E4%BB%98%E7%BB%93%E6%9E%9C%E9%80%9A%E7%9F%A5) |
| 微信支付官方文档-通知返回参数 | [link](https://pay.weixin.qq.com/wiki/doc/apiv3/apis/chapter3_1_5.shtml) |

[**代码示例**](https://www.easywechat.com/4.x/payment/notify.html#%E6%94%AF%E4%BB%98%E7%BB%93%E6%9E%9C%E9%80%9A%E7%9F%A5)

```php
use EasyWeChat\Factory;

$config = [
    // 必要配置
    'app_id'             => 'xxxx',
    'mch_id'             => 'your-mch-id',
    'key'                => 'key-for-signature',   // API v2 密钥 (注意: 是v2密钥 是v2密钥 是v2密钥)

    // 如需使用敏感接口（如退款、发送红包等）需要配置 API 证书路径(登录商户平台下载 API 证书)
    'cert_path'          => 'path/to/your/cert.pem', // XXX: 绝对路径！！！！
    'key_path'           => 'path/to/your/key',      // XXX: 绝对路径！！！！

    'notify_url'         => '默认的订单回调地址',     // 你也可以在下单时单独设置来想覆盖它
];

$app = Factory::payment($config);

# 通知处理 付款状态
$response = $app->handlePaidNotify(function ($message, $fail) {
    // 你的逻辑
    
    return true;
    // 或者错误消息
    $fail('Order not exists.');
});

$response->send(); // Laravel 里请使用：return $response;
```

通常我们的处理逻辑大概是下面这样（**以下只是伪代码**）：

```php
$response = $app->handlePaidNotify(function($message, $fail){
    // 使用通知里的 "微信支付订单号" 或者 "商户订单号" 去自己的数据库找到订单
    $order = 查询订单($message['out_trade_no']);

    if (!$order || $order->paid_at) { // 如果订单不存在 或者 订单已经支付过了
        return true; // 告诉微信，我已经处理完了，订单没找到，别再通知我了
    }
    ///////////// <- 建议在这里调用微信的【订单查询】接口查一下该笔订单的情况，确认是已经支付 /////////////
    if ($message['return_code'] === 'SUCCESS') { // return_code 表示通信状态，不代表支付状态
        // 用户是否支付成功
        if (array_get($message, 'result_code') === 'SUCCESS') {
            $order->paid_at = time(); // 更新支付时间为当前时间
            $order->status = 'paid';
        // 用户支付失败
        } elseif (array_get($message, 'result_code') === 'FAIL') {
            $order->status = 'paid_fail';
        }
    } else {
        return $fail('通信失败，请稍后再通知我');
    }
    $order->save(); // 保存订单
    return true; // 返回处理完成
});
$response->send(); // return $response;
```

**实例**

```php

    public function setPaymentNoticeCallback(array $data)
    {
        $response = $this->app->handlePaidNotify(function ($message, $fail) {
            $orderRes = $this->app->order->queryByOutTradeNumber($message['out_trade_no']);
            Log::info(['message' => '查询订单编号' . $message['out_trade_no'] . '回调记录', 'res' => $orderRes]);
            if ($orderRes['result_code'] == 'SUCCESS' && $orderRes['trade_state'] != "SUCCESS") { # 如果查询订单没有支付不进行以下操作(为了防止别人伪造回调请求)
                Log::error('订单未支付,非法调用异步回调');
                return true;
            }
            // 你的逻辑
            Log::info('微信订单回调记录:' . json_encode($message));
            $order_number = !empty($message['out_trade_no']) ? $message['out_trade_no'] : "";
            Db::startTrans();
            $orderPaymentModel = OrderPaymentModel::where(['order_number' => $order_number])->lock(true)->find();
            $orderModel = OrderModel::where('id', $orderPaymentModel->order_id)->find();
            //如果订单状态已经是已支付状态直接返回成功
            if (!empty($order_number) && $orderPaymentModel->status == OrderPaymentModel::STATUS_YES_PAY) {
                Log::info('该订单已经支付完毕,' . '支付单id:' . $orderPaymentModel->id . ',支付单类型:' . $orderPaymentModel->type);
                return true;
            }

            #修改支付单支付状态
            $updateOrderPaymentData = [
                'pay_type' => OrderPaymentModel::PAY_TYPE_WECHAT,//微信支付
                'transaction_id' => $message['transaction_id'],//商户单
                'all_text' => $message //所有回调
            ];
            $orderPaymentModelBool = $this->updateOrderPaymentModelPaymentStatus($orderPaymentModel, $updateOrderPaymentData);
            # 修改订单模型支付状态
            $updateOrderData = ['payment_price' => $orderPaymentModel->payment_price];//支付金额
            $updateOrderModelBool = $this->updateOrderModelPaymentStatus($orderModel, $updateOrderData);
            # 如果支付单&订单模型对应状态修改成功
            if ($orderPaymentModelBool && $updateOrderModelBool) {
                # 如果当前的订单是尾款或者全款的情况下添加可以评论信息
                if ($orderPaymentModel->type == OrderPaymentModel::TYPE_TAIL_PAYMENT || $orderPaymentModel->type == OrderPaymentModel::TYPE_TAIL_FULL_PAYMENT) {
                    $bool = (new EvaluationService())->addEvaluation($orderPaymentModel->order_id);
                }
                Db::commit();
                return true;
            }
            Db::rollback();
            // 或者错误消息
            $fail('Order not exists.');
        });

        $response->send(); // Laravel 里请使用：return $response;
    }
```

## [退款](https://www.easywechat.com/4.x/payment/refund.html#%E7%94%B3%E8%AF%B7%E9%80%80%E6%AC%BE)

**资料**

| 名称             | 地址                                                         |
| ---------------- | ------------------------------------------------------------ |
| easywechat-退款  | [link](https://www.easywechat.com/4.x/payment/refund.html#%E7%94%B3%E8%AF%B7%E9%80%80%E6%AC%BE) |
| 微信退款官方文档 | [link](https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=9_4) |

**代码示例**

> 数组中的参数具体可以参考 https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=9_4
>
> **注意事项**
>
> 订单金额&退款金额要*100 因为微信是以分为单位

**根据微信订单号退款**

```php
# 参数分别为：微信订单号(微信自己的订单号不是我们自定义的订单号)、商户退款单号(可以自定义)、订单金额(一定要以支付金额为准)*100、退款金额*100、其他参数
$result = $app->refund->byTransactionId('4200001382202204107379924807', '456456478798', 1*100, 1*100, [
            // 可在此处传入其他参数，详细参数见微信支付文档
            'refund_desc' => 'asd', # 退款原因
        ]);
```

**根据商户订单号退款**

```php
// 参数分别为：商户订单号(我们自己定义的订单号)、(可以自定义)、订单金额(一定要以支付金额为准)、退款金额、其他参数
$app->refund->byOutTradeNumber(string $number, string $refundNumber, int $totalFee, int $refundFee, array $config = []);

// Example:
$result = $app->refund->byOutTradeNumber('out-trade-no-xxx', 'refund-no-xxx', 20000*100, 1000*100, [
    // 可在此处传入其他参数，详细参数见微信支付文档
    'refund_desc' => '退运费', # 退款原因(string 80 字数不能过长)
]);
```

![1649817302(1).jpg](https://s2.loli.net/2022/04/13/vXah2yfmHU538Ae.png)



## bug解析

### [无法将输入源“/body/xml/total_fee”映射到目标字段“标价金额”中，此字段需要一个合法的 64 位有符号整数](https://genjiejie.blog.csdn.net/article/details/116932411?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_default&utm_relevant_index=1)

> 微信支付是以分为单位假如你计算出来的价格是**50** 那么支付的时候价格就是**0.5** 所以你支付的价格要*100才是真实价格



### 只要调起支付同一个订单号2小时内只要没有支付成功都可以重复调起支付,两小时后会失效问题（201 商户订单号重复）

>**201复现**
>
>1. 调起支付后没有进行支付(**退出支付**),然后进行改价或者别的信息等操作订单信息与上次订单id信息提交不一致(**一般都是这种情况**)
>2. 调起支付后**没有**进行支付(**退出支付**)，两小时后再次支付(**过期**)
>
>**微信取消支付再二次/多次支付及201商户订单号重复解决思路**
>
>最简单解决办法:再次支付请求统一下单接口时，务必保持待支付的订单号（即商户订单号，out_trade_no），商品描述字段(即商品描述，body)和上次请求的内容一致(比如:参数数量，我是附加数据参数(即附加数据，attach)第一次没有,第二次添加了,两次提交的参数不一致,导致的订单号重复)。另外，如果价格改变，也不能重复提交，只能重新生成订单号，重新向微信发起支付请求…
>
>https://blog.csdn.net/weixin_42506139/article/details/120843958
>
>https://developers.weixin.qq.com/community/pay/doc/0002c42c090428c026ac6deff5b800?jumpto=comment&commentid=000ce409f7cbd0002eac2031153c

**后期发现上诉问题解决了但是本地开发环境调起支付之后，测试服小程序那边无法调起(201错误)，反之测试服小程序先调起本地开发就无法调起支付**

> 由于订单id那边我做了**2小时过期处理**所以不会存在订单编号错误问题,价格只有后台才可以改价,但是改价之后我也会修改订单编号
>
> 所以问题只会出现在**notify_url** 这一段，由于**tp5**中我采用了动态获取域名(**request()->domain()**)所以初步怀疑是这个问题导致 开发测试环境提交信息不一致，手动修改域名一致情况下果然两边都可以请求**通**所以不是什么大问题

```php
 $result = $this->app->order->unify([
            'body' => $body,
            'out_trade_no' => $order_number,
            'total_fee' => $payment_price * 100,
            //'spbill_create_ip' => '123.12.12.123', // 可选，如不传该参数，SDK 将会自动获取相应 IP 地址
            'notify_url' => request()->domain() . '/api/order_payment_notice_callback', // 支付结果通知网址，如果不设置则会使用配置里的默认地址(换成自己的通知回调地址)
            'trade_type' => 'JSAPI',
            'openid' => $openId, //用户自己的openid
        ]);
```

### 例如拼团:参与拼团到了输入密码支付页面先不输入密码，先修改拼团结束时间，让拼团结束后，在输入密码支付，能支付成功和拼团成功，在查看拼团详情，所需成团人数和实际参团人数不一致

**参考**

| 名称             | 地址                                                         |
| ---------------- | ------------------------------------------------------------ |
| 设置微信支付过期 | [link](http://www.sanshu.cn/article/12248.html)  [link](http://www.sanshu.cn/article/12248.html) |
| 微信官方支付文档 | [link](https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=9_1) |



> 即用户卡在支付页面不支付，然后拼团时间过期了再支付这种情况就需要限制一下支付时间了
>
> **注意事项**    time_start  time_expire 两个参数缺一不可

**代码示例**

```php
#------------ easywechat 调用微信支付设置过期时间
if (!empty($is_spell_group) && $is_spell_group == OrderModel::IS_SPELL_GROUP_YES && $spell_group_user_pid != SpellGroupUsersModel::PARENT_ID_DEFAULT) {
            Log::info("此订单是拼团订单需要设置订单过期时间,订单过期时间为:".$time_expire.',订单id:'.$order_id);
            $result = $this->app->order->unify([
                'body' => $body,
                'out_trade_no' => $order_number,
                'total_fee' => $payment_price * 100,
                //'spbill_create_ip' => '123.12.12.123', // 可选，如不传该参数，SDK 将会自动获取相应 IP 地址
                'notify_url' => request()->domain() . '/api/order_payment_notice_callback', // 支付结果通知网址，如果不设置则会使用配置里的默认地址(换成自己的通知回调地址)
                'trade_type' => 'JSAPI',
                'openid' => $openId, //用户自己的openid
                'time_start'=>date('YmdHis'),
                'time_expire' => $time_expire,
            ]);
        } else {
            # 非拼团支付
            $result = $this->app->order->unify([
                'body' => $body,
                'out_trade_no' => $order_number,
                'total_fee' => $payment_price * 100,
                //'spbill_create_ip' => '123.12.12.123', // 可选，如不传该参数，SDK 将会自动获取相应 IP 地址
                'notify_url' => request()->domain() . '/api/order_payment_notice_callback', // 支付结果通知网址，如果不设置则会使用配置里的默认地址(换成自己的通知回调地址)
                'trade_type' => 'JSAPI',
                'openid' => $openId, //用户自己的openid
            ]);
        }
```

