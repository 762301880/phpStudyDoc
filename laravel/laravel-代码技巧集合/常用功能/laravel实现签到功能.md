# 一、思路

> 签到功能,比如用户每天在游戏公会只可以签到一次,
>
> 签到以后不可以重复签到，
>
> 第二天的时候又可以继续签到

## 1.1数据库设计`DDL`

```sql
CREATE TABLE `user_society_sign` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_id` int(11) DEFAULT NULL COMMENT '签到的用户id',
  `society_id` int(11) DEFAULT NULL COMMENT '工会id',
  `created_at` timestamp NULL DEFAULT NULL COMMENT '签到时间',
  `updated_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='用户工会签到表';
```



# 二、逻辑设计

```php
use Illuminate\Http\Request;
use App\Models\UserSocietySign;
use Carbon\Carbon;
public function sign(Request $request)
{
     # 判断用户是否已经加入公会如果没有加入返回错误信息(此代码忽略)
     #登陆思路就是如果查询表中用户的id存在并且日期是今天那么就是已经签到成功,否则的话给予签到逻辑
     $isCreate=false;
     $isNull=UserSocietySign::whereDate('created_at',Carbon::now()->toDate())
         ->where('user_id',$request->user_id)
         ->first();       
     if($isNull==null){
        $userSocietySign=new UserSocietySign();
        $userSocietySign->user_id=$request->user_id;
        $userSocietySign->user_id=$request->society_id;
        $isCreate=$userSocietySign->save;
     }
    return response()->json(['bool'=>$isCreate,'message'=>"签到成功!"])
}
```

