# 一、说明

> 本次开发任务接到了关于用户登录社团界面就会广播给本社团的全部在线人员
>
> 谁谁谁已经登录并带出社团身份的功能，本来想使用socket去实现此功能，奈何
>
> 技术主管&前端对接小伙伴强烈要求使用轮询所以本次采用轮询实现

# 二、代码逻辑

## 2.1 前端采用后端轮询的方式实现

### 2.1.1 存储用户信息到`redis`（此处后端依据api接口使用事件触发用户进入到社团事件）

- 事件`SignClub`登录社团主逻辑

```php
    # 定义需要传递的参数 参数1 当前的用户主键 参数2. 社团主键
    public $user_id;
    public $club_id;
    public function __construct($user_id,$club_id)
    {
        //
        $this->user_id = $user_id;
        $this->club_id = $club_id;
    }
```



- 事件的监听者`SignClubSaveInformation`中逻辑

```php
use Illuminate\Support\Facades\Redis;
 
public function handle(SignClub $event)
    {
        //查询社团用户是否存在
        $clubMember = ClubMember::where(['club_id' => $event->club_id, 'user_id' => $event->user_id, 'status' => 1])->first();
        if ($clubMember) {
         //如果存在的话构建用户信息记录
            $data = [
                'id' => $clubMember->id,
                'level' => ClubGrowthTitle::getClubGrowthTitle($clubMember->growth_point) ?? "",
                'user_id' => $clubMember->user_id??'',
                'position' => $clubMember->position ?? "",
                'nickname' => $clubMember->user->nickname ?? "",
                'student_name' => $clubMember->user->student->stu_name ?? "",
            ];
            //保存为json数据
            $data=json_encode($data,true);
            //存入redis
            $redis = Redis::connection();
            $redis->lpush('club:' . $event->club_id, [$data]);
        }
    }
```

### 2.1.2 从`redis`将用户信息读取出来并删除`redis`中的用户数据(此处前端需要轮询)

```php
use Illuminate\Support\Facades\Redis;
 
public function getSignClub(Request $request)
    {
        try {
            if ($request->input('id') == null) {
                throw new ApiException('社团主键不能为空');
            }
            $redis = Redis::connection();
            $data = $redis->lrange('club:' . $request->id, 0, -1);
            $count = count($data);
            static $values=[];
            foreach ($data as $key=>$value) {
                //循环处理数据
                $values[]=(object)json_decode($data[$key],true);
                //移除redis值
                $redis->rpop('club:' . $request->id);
                $count--;
            }
            return $this->success($values, '用户信息返回成功');
        } catch (ApiException $apiException) {
            return $this->error($apiException->getMessage());
        }
    }
```



