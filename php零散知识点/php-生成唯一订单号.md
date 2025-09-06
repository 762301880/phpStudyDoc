# 资料

| 名称                     | 地址                                                         |
| ------------------------ | ------------------------------------------------------------ |
| 微信支付官网(商户订单号) | [link](https://pay.weixin.qq.com/wiki/doc/api/native.php?chapter=4_2)  [link](https://developer.aliyun.com/article/868776) |
|                          |                                                              |

## 商户订单号 生成规则

> 商户支付的订单号由商户自定义生成，仅支持使用字母、数字、中划线-、下划线_、竖线|、星号*这些英文半角字符的组合，请勿使用汉字或全角等特殊字符。微信支付要求商户订单号保持唯一性（建议根据当前系统时间加随机序列来生成订单号）。重新发起一笔支付要使用原订单号，避免重复支付；已支付过或已调用关单、撤销（请见后文的API列表）的订单号不能重新发起支付。
>
> 我们主要记住生成的订单编号必须是唯一不重复的编号其他的可以根据喜好生成

**代码生成示例**

> 自定义订单编号生成规则**日期+用户id+随机数字4位**
>
> UUID（通用唯一识别码）是一种用于在计算机系统中唯一标识实体（如文件、对象、资源等）的标准化方式。UUID的设计目的是保证在分布式系统中生成的标识符是唯一的，即使是在不同的系统中。
>
> 以下是UUID的一些详细解释：
>
> 1. **格式**：
>    - UUID通常以32个十六进制数字的形式表示，共有5个“部分”组成，用连字符分隔。例如：`550e8400-e29b-41d4-a716-446655440000`。
>
> 2. **版本**：
>    - UUID有不同的版本，每个版本有不同的生成规则和格式。最常见的版本是基于时间戳的版本1和基于随机数的版本4。
>
> 3. **版本1**（基于时间戳）：
>    - 这种UUID基于当前时间和计算机的MAC地址生成。因此，同一台计算机上生成的UUID在正常情况下是唯一的。
>
> 4. **版本4**（基于随机数）：
>    - 这种UUID完全基于随机数生成。因此，即使在不同的系统中生成，也极其不可能出现冲突。
>
> 5. **唯一性**：
>    - UUID的唯一性取决于生成它们的算法和数据源。对于版本1，如果在同一时钟周期内生成多个UUID，可能会出现冲突。对于版本4，理论上是无法出现冲突的。
>
> 6. **生成算法**：
>    - 不同版本的UUID使用不同的生成算法。例如，版本1使用了时间戳和MAC地址，而版本4完全基于随机数。
>
> 7. **用途**：
>    - UUID在许多计算机领域都有广泛的用途，例如在分布式系统中确保唯一标识、数据库中的主键、Web开发中的会话标识等。
>
> 8. **性能考虑**：
>    - 生成版本1的UUID可能涉及到与系统时钟和MAC地址相关的操作，这可能会带来一些性能开销。相比之下，生成版本4的UUID通常更快速简单。
>
> 9. **安全性**：
>    - 版本1的UUID可能会因为暴露MAC地址而带来一些安全风险，特别是在某些敏感环境下。版本4基于完全随机数，通常被认为是更安全的。
>
> 10. **实现**：
>     - 大多数编程语言和平台都提供了生成UUID的库或函数，以便开发者能够轻松地在他们的应用程序中使用UUID。
>
> 总的来说，UUID是一个非常有用的工具，特别是在需要保证在分布式环境中生成的标识符是唯一的情况下。不同版本的UUID适用于不同的应用场景，开发者可以根据具体需求选择合适的版本。

```php
public function generateOrderNumber(){
    $timestamp = time(); // 获取当前时间戳
    $randomNumber = rand(1000, 9999); // 生成一个4位随机数
    return date('YmdHis') . $randomNumber; // 组合时间戳和随机数
 }
```

## 利用uuid生成唯一订单编号

```php
# 示例-uuid当作key  
public function randNum($type=null)
    {
        mt_srand((double)microtime() * 10000);
        $charid = strtolower(md5(uniqid(rand(), true)));
        $hyphen = '-';
        $uuidV4 =
            substr($charid, 0, 8) . $hyphen .
            substr($charid, 8, 4) . $hyphen .
            substr($charid, 12, 4) . $hyphen .
            substr($charid, 16, 4) . $hyphen .
            substr($charid, 20, 12);
        return $uuidV4;
    }
# 示例-2
public function randNum($type=null){
  /**
   * 唯一id+随机数
   */
  return uniqid('ver_id',false).rand(0000,9999);
}
```

#  补充 

##  业务逻辑防止订单号重复

> 防止 **订单号重复** 一般需要从 **生成策略** 和 **数据库约束** 两方面入手。

## 1. 数据库层保证（最可靠）

- 在订单表 `order_number` 字段上加 **唯一索引（UNIQUE KEY）**

  ```sql
  ALTER TABLE orders ADD UNIQUE (order_number);
  ```

- 即使高并发时多个请求生成相同的订单号，插入时也会报错，你可以在代码里 **捕获唯一约束异常**，然后重新生成。

------

## 2. 订单号生成策略(推荐)

常见方案：

### 方案A：时间戳 + 随机数(很大几率会重复)

```php
$orderNo = date('YmdHis') . mt_rand(1000, 9999);
```

缺点：并发很高时，仍然有概率撞号，所以必须配合数据库唯一索引。

### 方案B：时间戳 + 自增ID

- 使用数据库的自增ID（或雪花算法/Redis自增）来保证唯一。

- 例如：

  ```php
  $id = getNextIdFromRedisOrDB(); // 唯一递增
  $orderNo = date('YmdHis') . str_pad($id, 6, '0', STR_PAD_LEFT);
  ```

  **getNextIdFromRedisOrDB**

    ```php
class OrderNumberGenerator
{
    protected $redis;
    protected $prefix = 'order_no:';

    public function __construct($redis)
    {
        $this->redis = $redis;
    }

    public function generate()
    {
        // 以当天日期作为 key，避免数字无限增长
        $date = date('Ymd');
        $key = $this->prefix . $date;

        // Redis 自增
        $incr = $this->redis->incr($key);

        // 设置过期时间，避免 key 无限累积（比如 2 天）
        if ($incr === 1) {
            $this->redis->expire($key, 2 * 24 * 3600);
        }

        // 拼接订单号：日期 + 6位递增数
        return $date . str_pad($incr, 6, '0', STR_PAD_LEFT);
    }
}
    ```

调用：

```php
$redis = new Predis\Client();
$generator = new OrderNumberGenerator($redis);
$orderNo = $generator->generate();

echo $orderNo; // 20250906000001
```

## 3. redis去重

```php
# 约定订单编号生成规则
$date = date('Ymd');
$orderNo = date('YmdHis') . str_pad($id, 6, '0', STR_PAD_LEFT);

# 每次生成订单编号先存入redis
$key = $date.':'.str_pad($id, 6, '0', STR_PAD_LEFT);
$this->redis->expire($key, 2 * 24 * 3600); # 设置过期时间 
# 然后每次生成直接比较redis是否存在如果存在重新生成
```



