## 说明

> RBAC（Role-Based Access Control）是一种基于角色的访问控制机制，它通过将权限授予给角色，然后将角色授予给用户，从而实现了用户对资源的权限控制。
>
> 1. **核心概念**
> 用户 (User): 系统中的操作者。
> 角色 (Role): 一组权限的集合，代表了某种职责或功能。
> 权限 (Permission): 对特定资源执行特定操作的能力。
> 资源 (Resource): 系统中可被访问的对象。
> 2. **关系定义**
> 用户-角色关系: 一个用户可以拥有多个角色。
> 角色-权限关系: 一个角色可以包含多个权限。
> 权限-资源关系: 一个权限可以对应多个资源上的操作



> 在使用 TP5 实现 RBAC 权限管理时，可以按照以下步骤进行:
>
> 1. 创建数据表
>    创建用户表（user）和角色表（role），以及用户角色关联表（user_role）和角色权限关联表（role_access）。
> 2. 定义模型
>    在 TP5 中，可以使用模型来管理数据库。根据上述数据表，定义用户模型（User）、角色模型（Role）以及它们之间的关联模型（UserRole 和 RoleAccess）。
> 3. 编写控制器
>    在 TP5 中，控制器用于接收和处理请求，并控制页面的显示。编写一个权限控制器（Access），用于对用户进行权限认证。
> 4. 使用中间件
>    中间件是 TP5 中非常重要的概念，它可以在请求到达控制器之前或之后执行代码逻辑。使用中间件进行权限验证，可以在控制器之前对请求进行处理。
> 5. 创建页面
>    最后，根据需要创建相应的页面和功能，并根据用户角色和权限进行访问控制。
>
> 以上是使用 TP5 实现 RBAC 权限管理的基本步骤，具体实现的代码可以根据自己的需求和情况进行编写。

### 数据表设计

1. 用户表（user）

- id：用户 ID
- username：用户名
- password：密码
- email：电子邮件
- last_login_time：最后登录时间
- last_login_ip：最后登录 IP

1. 角色表（role）

- id：角色 ID
- name：角色名称
- description：角色描述

1. 用户角色关联表（user_role）

- id：关联 ID
- user_id：用户 ID
- role_id：角色 ID

1. 权限表（access）

- id：权限 ID
- name：权限名称
- description：权限描述

1. 角色权限关联表（role_access）

- id：关联 ID
- role_id：角色 ID
- access_id：权限 ID

### 代码逻辑

1. 创建模型

- UserModel：对应用户表的模型
- RoleModel：对应角色表的模型
- UserRoleModel：对应用户角色关联表的模型
- AccessModel：对应权限表的模型
- RoleAccessModel：对应角色权限关联表的模型

1. 编写控制器

- AccessController：用于根据用户角色和权限进行访问控制。
  - index：展示页面或跳转到其他控制器
  - checkLogin：检查用户是否登录
  - checkAccess：检查用户是否有访问权限

1. 定义中间件

- AuthMiddleware：用于检查用户是否登录。

1. 创建页面

- 根据用户角色和权限进行访问控制，展示或隐藏相应的页面和功能。

以上是一个简单的 RBAC 权限管理系统的 TP5 代码逻辑和数据表设计，具体实现的代码可以根据自己的需求和情况进行编写。



# 自动权限管理功能研究

## 定义路由

> is_ctl  参与权限控制

```shell
Route::group('api/pxs', function () {
    //app 业务模块
    Route::group('appbss', function () {
        //抖音客户
        Route::group('dy_customer',function (){

            //合同
            Route::post('signedNo', 'appbusiness/dy_message.CustomerSigned/signedNo')->option(['route_txt'=>"APP抖音客户管理/合同模块/获取合同编号","is_ctl"=>0]);
            .....
```



## 自动填充数据结构

> 代码取自`项目\thinkphp\library\think\console\command\RouteList.php`  **getRouteList**方法

```shell
<?php
/**
 * Created by PhpStorm.
 * User: joy
 * Date: 19-3-10
 * Time: 下午8:43
 */

namespace app\common\command;


use app\sys\model\Permission;
use think\console\Command;
use think\console\Input;
use think\console\Output;
use think\console\Table;
use think\Container;

class Perm extends Command
{
    protected function configure()
    {
        $this->setName('pxs:permission')
            ->setDescription('自动创建权限表');
    }

    protected function execute(Input $input, Output $output)
    {
        Container::get('route')->setTestMode(true);
        // 路由检测
        $path = Container::get('app')->getRoutePath();

        $files = is_dir($path) ? scandir($path) : [];

        foreach ($files as $file) {
            if (strpos($file, '.php')) {
                $filename = $path . DIRECTORY_SEPARATOR . $file;
                // 导入路由配置
                $rules = include $filename;

                if (is_array($rules)) {
                    Container::get('route')->import($rules);
                }
            }
        }

        if (Container::get('config')->get('route_annotation')) {
            $suffix = Container::get('config')->get('controller_suffix') || Container::get('config')->get('class_suffix');

            include Container::get('build')->buildRoute($suffix);
        }

        $routeList = Container::get('route')->getRuleList();        # 实际上只要这一行就够了
//        file_put_contents('route_list.php', json_encode($routeList));
//        dd($routeList[""][0]);
    }
}
```

**打印出来的数据**

```php
[
  "domain" => ""
  "method" => "post"
  "rule" => "api/pxs/appbss/dy_customer/signedNo"
  "name" => "appbusiness/dy_message.CustomerSigned/signedNo"
  "route" => "appbusiness/dy_message.CustomerSigned/signedNo"
  "pattern" => []
  "option" => array:2 [
    "route_txt" => "APP抖音客户管理/合同模块/获取合同编号"
    "is_ctl" => 0
  ]
]    
```





## **解析为三级分类**

> [文件下载](https://yaoliuyang.lanzoul.com/iKETa2jekpvc)

```php
[
    "线索管理/官网资源池/允许跟进部门",
    "线索管理/官网资源池/详情",
    "App客户管理/我的客户/分配",
    "App客户管理/我的客户/退回",
]
# 具体文件请下载  https://yaoliuyang.lanzoul.com/iKETa2jekpvc
```

**ai实现**

```php
<?php
$data = [
    "财务管理/合同管理/添加合同",
    "财务管理/合同管理/删除合同",
    "财务管理/合同管理/修改合同",
    "财务管理/合同管理/查询合同",
    "线索管理/官网资源池/添加找店",
    "线索管理/官网资源池/删除找店",
    "线索管理/官网资源池/修改找店",
    "财务管理/合同管理/编辑合同",
    "电子签管理/模板管理/模板删除",
    "电子签管理/模板管理/模板添加",
    "电子签管理/模板管理/模板修改",
    "财务管理/单独退款/单独退款添加",
    "线索管理/线索列表/列表",
    "线索管理/线索列表/添加",
    "线索管理/模板管理/列表",
];

function parseData($data) {
    $result = [];

    foreach ($data as $item) {
        $parts = explode('/', $item);
        $level1 = $parts[0];
        $level2 = $parts[1];
        $level3 = $parts[2];

        if (!isset($result[$level1])) {
            $result[$level1] = ["label" => $level1, "children" => []];
        }

        $level1Children = &$result[$level1]["children"];

        if (!isset($level1Children[$level2])) {
            $level1Children[$level2] = ["label" => $level2, "children" => []];
        }

        $level2Children = &$level1Children[$level2]["children"];

        $level2Children[] = ["label" => $level3];
    }

    // Convert to sequential array
    $result = array_values($result);
    foreach ($result as &$level1) {
        $level1["children"] = array_values($level1["children"]);
    }

    return $result;
}

$parsedData = parseData($data);

// Output the parsed data
echo json_encode($parsedData, JSON_UNESCAPED_UNICODE | JSON_PRETTY_PRINT);
?>


# 以上代码精简

<?php
$data = [
    "财务管理/合同管理/添加合同",
    "财务管理/合同管理/删除合同",
    "财务管理/合同管理/修改合同",
    "财务管理/合同管理/查询合同",
    "线索管理/官网资源池/添加找店",
    "线索管理/官网资源池/删除找店",
    "线索管理/官网资源池/修改找店",
    "财务管理/合同管理/编辑合同",
    "电子签管理/模板管理/模板删除",
    "电子签管理/模板管理/模板添加",
    "电子签管理/模板管理/模板修改",
    "财务管理/单独退款/单独退款添加",
    "线索管理/线索列表/列表",
    "线索管理/模板管理/列表",
];

function parseData($data) {
    $result = [];

    foreach ($data as $item) {
        $parts = explode('/', $item);
        $ref = &$result;

        foreach ($parts as $part) {
            if (!isset($ref[$part])) {
                $ref[$part] = ["label" => $part, "children" => []];
            }
            $ref = &$ref[$part]["children"];
        }
    }

    $format = function ($node) use (&$format) {
        return array_values(array_map(function ($child) use ($format) {
            return ["label" => $child["label"], "children" => $format($child["children"] ?? [])];
        }, $node));
    };

    return $format($result);
}

$parsedData = parseData($data);

// Output the parsed data
echo json_encode($parsedData, JSON_UNESCAPED_UNICODE | JSON_PRETTY_PRINT);
?>


```

