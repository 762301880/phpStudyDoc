#  查询的数据指定多少日期之后才显示

> 例如我想让用户第二天才可以看见第一天的中奖数据，数据库中保存的**data** 格式为***2021-10-25***这种类型
>
> 先转化为unix时间戳然后加上需要什么时候查看的时间直接<=当前日期即可

```shell
        $gainPrizeList = $this->gainPrizeList($request)
            ->when(!empty($request->input('date')) && $request->input('type') == PullNewGainPrizeModel::TYPE_FALSE, function ($query) {
                // 第一天导入的第二天 0:30 才显示
                $today = (strtotime(date('Y-m-d H:i:s')));
                $oneDay = 60 * 60 * 24;
                $expirationTime = $oneDay + 1800; //过期时间
                $query->whereRaw("UNIX_TIMESTAMP(date) + $expirationTime <= $today"); # 核心逻辑
            })
            ->paginate($request->input('limit', 999));
```

