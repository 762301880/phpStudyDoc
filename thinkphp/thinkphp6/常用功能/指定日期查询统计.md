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
  if (getMonthDate() != false) {
            //跨月查询
            $start_time = getMonthDate()[0];
            $end_time = getMonthDate()[1];
            $query = StoreOrderStatistical::where('type', PvUvEnum::TYPE_TO_STORE)
                ->field("DATE_FORMAT(created_at,'%Y-%m') as date,sum(pv) as pv,sum(uv) as uv")
                ->where("DATE_FORMAT(created_at,'%Y-%m') BETWEEN '$start_time' AND '$end_time'  ") # 核心代码逻辑
                ->group("DATE_FORMAT(created_at,'%Y-%m')");
            //->having("date between '$start_time' and '$end_time'");
        } else {
            //非跨月查询
            $query = $this->getStoreOrderStatisticalQuery($request);
        }
        //excel导出
        if ($request->param('export') != null) {
            return $this->toStore->exportFlowExcel($query);
        }
        $toStoreList = $query->paginate($request->param('pagesize', 10));//到店统计列表
        $this->error('到店流量统计返回成功', '', compact('toStoreList'));
    }
# 全局辅助函数getMonthDate
if (!function_exists('getMonthDate')) {
    function getMonthDate()
    {
        $start_time = request()->param('start_time');
        $end_time = request()->param('end_time');
        $start_str = substr($start_time, 5, 2);
        $end_str = substr($end_time, 5, 2);
        if ($start_str != $end_str && $start_str < $end_str) {
            return array($start_time, $end_time);
        } else {
            return false;
        }
    }
}
```