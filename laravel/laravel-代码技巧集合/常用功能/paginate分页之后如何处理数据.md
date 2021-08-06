#  说明



参考[资料](https://learnku.com/laravel/t/44709?)





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
        $orderDelivery->getCollection()->transform(function ($orderDelivery) {
            return new OrderDeliveryStatusDetails($orderDelivery);
        });
        return $orderDelivery;
```

