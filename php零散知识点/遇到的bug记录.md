#  对象未重复新建 重复调用



**看以下代码示例**

> 我的想法是   依赖注入直接一次把模型给赋值调用岂不是更方便   结果 循环里 调用 create方法 
>
> 发现重复的返回了 同一个id

```php
class ContactService extends CommonService
{
    protected $contactModel = null;

    public function __construct()
    {
        $this->contactModel = new ContactModel();
    }

    public function create($data, $return_id = false, $isFollowLog = true)
    {
        $contactModel = $this->contactModel;
        //....忽略字段
        $bool = $contactModel->save();
        if ($return_id) return $contactModel->id;  //这里直接返回了 id
        return $bool;
    }
}
```

**bug原因**

 问题的核心在于：
 **重复调用 `create()` 返回了同一个 ID** 

这通常说明 `$contactModel` **是同一个对象实例在重复使用**，没有在每次创建时重新实例化，导致第二次 `save()` 

直接更新了上一次保存的记录，而不是新建一条记录。

### 问题原因

在 `__construct()` 里写了：

```
$this->contactModel = new ContactModel();
```

这意味着 `$this->contactModel` 在整个 `ContactService` 生命周期中**只会实例化一次**。
所以当重复调用：

```
$this->create($data);
$this->create($data);
```

时，第二次 `$contactModel->save()` 实际上是**更新同一个对象**，而不是新建。
于是返回的 `$contactModel->id` 永远是第一次创建的 ID。

------

### ✅ 解决办法 1：每次创建新对象

在 `create()` 方法内部重新实例化 `ContactModel`：

```
public function create($data, $return_id = false, $isFollowLog = true)
{
    $contactModel = new ContactModel(); // ✅ 每次新建
    $contactModel->save($data);

    if ($return_id) {
        return $contactModel->id;
    }
    return true;
}
```

> 🔸这样每次调用 `create()` 都会生成新的记录，返回不同的 ID。

------

### ✅ 解决办法 2：复用时重置模型

如果你确实想复用 `$this->contactModel`（例如性能考虑），那你需要在保存前重置模型的状态：

```
public function create($data, $return_id = false, $isFollowLog = true)
{
    $this->contactModel->data([]); // 清空上次数据
    $this->contactModel->isUpdate(false); // 标记为新增

    $this->contactModel->save($data);

    if ($return_id) {
        return $this->contactModel->id;
    }
    return true;
}
```

------

### 🔧 建议

一般来说，像 `ContactService` 这种 Service 层不应该持有长期存在的模型实例，
 每次 `create()` 都 `new` 一个是最安全、最清晰的写法。