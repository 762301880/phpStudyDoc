#  说明



参考[资料](https://learnku.com/laravel/t/44709?)

参考[资料](https://blog.csdn.net/zhezhebie/article/details/117923576)





# 代码示例

```shell
        $status = $request->input('status');
        $orderDelivery = OrderDelivery::where('status', $status)
            ->when($status == OrderDelivery::STATUS_SUCCESS_DISTRIBUTION, function ($query) {
                //如果查询状态为4的时候返回当天24小时以内的订单
                $query->whereBetween('created_at', [date('Y-m-d') . ' 00:00:00', date('Y-m-d' . ' 23:59:59')]);
            })
            ->paginate($request->input('pageSize'));
        # 核心代码示例-直接调用 getCollection()  transform(传递闭包)
        # getCollection得到集合,tranfrom 方法是修改原值
        $orderDelivery->getCollection()->transform(function ($orderDelivery) {
            # response
            return new OrderDeliveryStatusDetails($orderDelivery);
        });
        return $orderDelivery;
        # 补充方法
            $orderDelivery->getCollection()->map(function ($orderDelivery) {
               $orderDelivery->字段='字段';
               return $orderDelivery;
          }

```

- response 类处理组装数据

```shell
<?php


namespace Plugins\Delivery\Responses;


use App\Models\Order;
use Carbon\Carbon;
use Plugins\Delivery\Models\OrderDelivery;

/**
 * @property integer serial_number 流水号
 */
class OrderDeliveryStatusDetails
{
    public function __construct(OrderDelivery $orderDelivery)
    {
        $this->order_id = $orderDelivery->order_id ?? "";//订单主键
        $this->serial_number = $orderDelivery->order->order_sn ?? "";//流水号（不知道具体的字段）
        $this->o2o_order_deliver_time = $orderDelivery->order->o2o_order_deliver_time ?? "";//配送时间
        $this->store_name = isset($orderDelivery->order->store) ? $orderDelivery->order->store->store_name : "";//商店名称
        $this->is_business = $this->getIsBusiness($orderDelivery->order);//是否营业
        $this->pickup_address = isset($orderDelivery->order->store) ? $orderDelivery->order->store->store_address : "";//取货地址
        $this->delivery_address = $orderDelivery->order->order_id ?? "";//送货地址（不知道具体的字段）
        $this->order_sn = $orderDelivery->order->order_sn ?? "";//订单编号
        $this->payment_time = Carbon::parse($orderDelivery->order->payment_time)->toDateTimeString() ?? "";//下单时间
        $this->payment_code = $orderDelivery->order->payment_code ?? "";//支付方式
        $this->delivery_time = $orderDelivery->order->payment_code ?? "";//预计送达时间（不知道具体的字段）
        $this->phone = $orderDelivery->o2oDistributor->o2o_distributor_phone ?? "";//骑手手机号
        return $this;
    }

    public function getIsBusiness(Order $order)
    {
        $startTimestamp = $order->store_o2o_open_start;//营业开始时间(分钟)
        $endTimestamp = $order->store_o2o_open_end;//营业结束时间(分钟)
        $thisTimestamp = Carbon::now()->timestamp;//当前时间戳
        if ($thisTimestamp >= $startTimestamp && $endTimestamp < $endTimestamp) {
            return true;
        }
        return false;
    }
}
```

