





参考 https://www.jianshu.com/p/3ea95c0fc814

# 使用

```php
$redis = new Client();
$date=date('Ymd');# 定义存储的名称
$redis->setbit($date,user_id,1);#  第二个参数是用户的id
dd($redis->bitcount($date));# 得到统计的个数
```

##  使用sadd函数实现

```php
# 核心二：使用redis的sAdd()方法
/**
 * sadd()命令将一个或多个成员元素加入到集合中，已经存在于集合的成员元素将被忽略。
 * 查询sadd()添加的个数sCard()
 **/
# 控制器代码示例
  public function setPvAndUv(Request $request)
    {
        $store_id = $request->input('store_id');
        if ($store_id == null) {
            throw new ErrorCodeException('商店主键不能为空');
        }
        $today = date('Ymd');
        $pvKey = "tw:pv:$store_id:$today"; //pvKey
        $uvKey = "tw:uv:$store_id:$today"; //uvKey
        $expiration_time = 3600 * 24 * 2;//过期时间2天
        //pv
        $isCrPv = $this->redis->incr($pvKey);
        $this->redis->expire($pvKey, $expiration_time);//设置redis过期时间
        //uv
        $isCrUv=$this->redis->sAdd($uvKey,56456);//参数2 user_id由于目前不知道此参数的具体位置所以写死代替
        $this->redis->expire($uvKey, $expiration_time);//设置redis过期时间
        return compact('isCrPv','isCrUv');
    }
# 自动同步代码示例
 public function __construct()
  {
        parent::__construct();
        $this->redis = Redis::connection()->client();
  }
   public function handle()
    {
        $store = Store::get(['store_id']);
        $date = date('Ymd');
        //构建昨天的时间戳
        $yesterday = strtotime(date('Y-m-d')) - 86400;
        $yesterday = date('Y-m-d', $yesterday);
        foreach ($store as $value) {
            $pvKey = "tw:pv:$value->store_id:$date"; //pvKey
            $uvKey = "tw:uv:$value->store_id:$date"; //uvKey
            //查询是本商店并且日期为昨天的数据
            $store = StoreOrderStatistical::firstOrNew(['store_id' => $value->store_id, 'date' => $yesterday]);
            $store->store_id = $value->store_id;
            $store->date = $yesterday;//昨天的数据
            $store->pv = $this->redis->get($pvKey) ?? 0;
            $store->uv = $this->redis->sCard($uvKey) ?? 0;
            $store->save();
        }
    }
```

