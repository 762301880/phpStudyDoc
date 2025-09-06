## **资料**

| name             | url                                                         |
| ---------------- | ----------------------------------------------------------- |
| 百度百科-DTO介绍 | [link](https://baike.baidu.com/item/DTO/16016821?fr=ge_ala) |

## 什么是DTO

> "DTO" 代表数据传输对象（Data Transfer Object）。
>
> DTO 是一种设计模式，用于在不同层（例如应用程序的不同组件或不同系统之间）之间传输数据。它的主要目的是将数据从一个地方传递到另一个地方，同时减少对数据的频繁访问。
>
> 通常情况下，DTO 用于在不同层或不同系统之间传递数据，以避免直接暴露内部实体对象的细节。这可以提高代码的灵活性和可维护性，因为如果内部实体的结构发生变化，只需调整DTO而不是所有依赖于该实体的代码。
>
> 举例来说，假设你有一个包含用户信息的应用程序。在数据传输过程中，你可以使用用户DTO来传递用户的姓名、电子邮件地址等信息，而不是直接传递整个用户对象。
>
> 总的来说，DTO 是一种常用的软件设计模式，可以帮助组织和管理数据在不同部分之间的传递，从而提高代码的灵活性和可维护性。

##  laravel项目中使用

在 Laravel 中使用 DTO（数据传输对象）是一种很好的做法，可以帮助将数据从控制器传递到服务层或模型，同时保持代码的清晰和可维护性。以下是一个简单的示例，演示了如何在 Laravel 中实现DTO以及相应的目录结构：

### 1. 目录结构：

假设你有一个名为 "User" 的模型，并且你想在控制器和服务层之间传递用户数据。

```php+HTML
/app
    /Http
        /Controllers
            UserController.php
    /Services
        UserService.php
    /DTO
        UserDTO.php
```

### 2. 创建 DTO：

在 `app/DTO` 目录下创建一个名为 `UserDTO.php` 的文件：

```php+HTML
<?php

namespace App\DTO;

class UserDTO
{
    public $name;
    public $email;
    
    public function __construct($name, $email)
    {
        $this->name = $name;
        $this->email = $email;
    }
}
```

### 3. 使用 DTO：

在你的控制器中，你可以创建一个 UserDTO 对象并将其传递给服务层：

```php+HTML
<?php

namespace App\Http\Controllers;

use App\Services\UserService;
use App\DTO\UserDTO;
use Illuminate\Http\Request;

class UserController extends Controller
{
    protected $userService;

    public function __construct(UserService $userService)
    {
        $this->userService = $userService;
    }

    public function store(Request $request)
    {
        $name = $request->input('name');
        $email = $request->input('email');
        
        $userDTO = new UserDTO($name, $email);
        $this->userService->createUser($userDTO);
        
        // ...
    }
}
```

### 4. 服务层使用 DTO：

在你的服务层中，接受 DTO 对象并处理它：

```php+HTML
<?php

namespace App\Services;

use App\DTO\UserDTO;
use App\User;

class UserService
{
    public function createUser(UserDTO $userDTO)
    {
        $user = new User();
        $user->name = $userDTO->name;
        $user->email = $userDTO->email;
        $user->save();
        
        return $user;
    }
}
```

### 总结：

在这个示例中，我们创建了一个名为 `UserDTO` 的数据传输对象，用于在控制器和服务层之间传递用户数据。控制器中使用 DTO 来组织和传递数据，服务层接受 DTO 对象并将其用于创建用户。这种做法可以使代码更加清晰和模块化，同时也提高了代码的可测试性和可维护性。

请注意，这只是一个简单的示例，实际上你可能会在 DTO 中包含更多的属性和方法，以便在不同部分之间传递更复杂的数据结构。