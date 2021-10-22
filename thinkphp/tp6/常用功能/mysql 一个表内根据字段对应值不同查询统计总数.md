# 说明

> 今天又一个功能用到了***mysql的高级函数***，功能是依据统计数据表
>
> 中一个字段对应值的个数，本来想着用代码拼接的方式去实现
>
> 功能但是想着这样要写好多代码想着能不能使用mysql的函数实现

# 资料

| name      | url                                                       |
| --------- | --------------------------------------------------------- |
| mysql函数 | [link](https://www.runoob.com/mysql/mysql-functions.html) |
|           |                                                           |

- 使用到的***mysql***函数

| 函数名                                                       | 描述                                                         | 实例                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| CASE expression     WHEN condition1 THEN result1     WHEN condition2 THEN result2    ...     WHEN conditionN THEN resultN     ELSE result END | CASE 表示函数开始，END 表示函数结束。如果 condition1 成立，则返回 result1, 如果 condition2 成立，则返回 result2，当全部不成立则返回 result，而当有一个成立之后，后面的就不执行了。 | SELECT CASE  　　WHEN 1 > 0 　　THEN '1 > 0' 　　WHEN 2 > 0 　　THEN '2 > 0' 　　ELSE '3 > 0' 　　END ->1 > 0 |



# 代码示例

- 直接粘贴过来了

```php
# 主要用到了 sum(case when identify_type=01 then 1 else 0 end ),   
public function ajaxOrderDate(Request $request)
    {
        if (getMonthDate() != false) {
            //如果是跨月查询
            $start_time = getMonthDate()[0];
            $end_time = getMonthDate()[1];
            //筛选到店的订单
            $query = OrderModel::where('sale_code', '<>', '')
                ->field('
               DATE_FORMAT(statistical_date,"%Y-%m") as statistical_date,
               count(order_id) as order_num,
               sum(case when payment_type="微信支付" then 1 else 0 end )as wechat_pay,
               sum(case when sale_status=1 then 1 else 0 end ) as been_use,  
               sum(case when sale_status=0 then 1 else 0 end ) as stay_use,
               sum(case when order_state=12 then 1 else 0 end )as single_back
               ')
                ->where("DATE_FORMAT(statistical_date,'%Y-%m') BETWEEN '$start_time' AND '$end_time'  ")
                ->group("DATE_FORMAT(statistical_date,'%Y-%m')");
        } else {
            $query = $this->toStore->getToStoreOrderQuery($request)
                ->field('
               statistical_date,
               count(order_id) as order_num,
               sum(case when payment_type="微信支付" then 1 else 0 end )as wechat_pay,
               sum(case when sale_status=1 then 1 else 0 end ) as been_use, 
               sum(case when sale_status=0 then 1 else 0 end ) as stay_use,
               sum(case when order_state=12 then 1 else 0 end )as single_back
               ')
                ->group('statistical_date');
        }
        $orderDate = $query->paginate($request->param('pagesize', 10));//到店统计列表
        // //excel导出
        if ($request->param('export') != null) {
            return $this->toStore->exportOrderExcel($orderDate,$request);
        }
        $this->error('订单统计返回成功', '', compact('orderDate'));
    }
# 补充说明
/**
 * 如果需要查询此状态的时候统计别的字段的值
 * 例如如果状态为1的时候统计jin
 */
sum(case when sale_status=1 then 需要合计的字段 else 0 end ) as been_use,  
SUM(IF(order_state in (12,13),order_amount,0)) as refund_amount,
sum(IF($indexField=$payment_time,order_amount,0)) as order_amount,
```

- 补充依赖于退款时间或者支付时间查询

```php
 $orders->getCollection()->map(function ($order) use ($request, $indexField) {
            $order->order_amount=$this->getTurnoverQuery($request)->where(function ($query)use ($order){
                $query->where('order_state', '<>', '1')->where('order_state', '<>', '0');
                $query->where("DATE_FORMAT(FROM_UNIXTIME(payment_time),'%Y-%m-%d')='{$order->statistical_date}'");
            })->sum('order_amount');
        });
```



