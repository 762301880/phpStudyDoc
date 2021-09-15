# 说明
> 假如数据库中只有一个时间字段created_at(2021-07-08 21:30:59),这种类型 但是我们
> 突然来了一个需求需要统计当月注册人数的统计，还好mysql内置的函数中有一个函数可以实现我们的
> 需求`DATE_FORMAT()`

# 参考资料
| 名词              | 地址                                                      |
| ----------------- | --------------------------------------------------------- |
| 菜鸟教程mysql函数 | [link](https://www.runoob.com/mysql/mysql-functions.html) |


## laravel代码使用示例
- 数据库

| id   | pv   | uv   | created_at          |
| ---- | ---- | ---- | ------------------- |
| 1    | 23   | 23   | 2021-08-04 10:29:38 |
| 2    | 45   | 365  | 2021-09-13 10:29:43 |
| 3    | 32   | 31   | 2021-05-12 10:29:46 |

- sql代码

```sql
SELECT
	DATE_FORMAT( created_at, '%Y-%m' ) AS date,
	sum( pv ) AS pv,
	sum( uv ) AS uv 
FROM
	`pv_uv` 
GROUP BY
	`date` 
HAVING
	date BETWEEN '2021-08' 
	AND '2021-10' 
ORDER BY
	`date` ASC	
```
- 逻辑代码

```php
DB::table('pv_uv')
            ->select(DB::raw("DATE_FORMAT(created_at,'%Y-%m') as date,sum(pv) as pv,sum(uv) as uv"))
            ->groupBy('date')
            ->having('date','2021-01')
//查询区间
//->havingBetween('date',[$request->input('start_time'),$request->input('end_time')]) 
            ->get();
## 等同于
 $start_time = $request->input('start_time');
        $end_time = $request->input('end_time');
        return DB::table('pv_uv')
            ->select(DB::raw("DATE_FORMAT(created_at,'%Y-%m') as date,sum(pv) as pv,sum(uv) as uv"))
            ->groupBy('date')
            ->when($request->input('start_time') && $request->input('end_time'), function ($query) use ($request) {
                $start_time = $request->input('start_time');
                $end_time = $request->input('end_time');
                $query->havingRaw(DB::raw("date between '$start_time' and '$end_time'"));
            })
            ->get();
```