> trait 是 PHP 提供的一种代码复用机制，允许在多个类之间共享方法和属性

## 示例

```shell
<?php


namespace app\common\traits;


use think\facade\Request;

trait CommonTraits
{

    public $staff_org = "";
    public $staff_duty = "";
    public $staff_number = "";
    public $staff_name = "";
    public $branch_id = "";

    /**
     * 初始化员工信息
     */
    public function initializeStaffInfo()
    {
        $_user = Request::param("_user");
        $this->staff_org = $_user->staff_org ?? "";
        $this->staff_duty = $_user->staff_duty ?? "";
        $this->staff_number = $_user->staff_number ?? "";
        $this->branch_id = $_user->branch_id ?? "";
        $this->staff_name = $_user->staff_name ?? "";
    }
}
```

**调用**

```php
class SomeController 
{
    use CommonTraits;

    public function __construct()
    {
        $this->initializeStaffInfo();
    }
}
```

