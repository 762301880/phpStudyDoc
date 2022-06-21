#  说明

> 开发过程中**paginate**自带返回的数据可能不是前端想要的例如data 想改为list 我们难道要每写一个方法都要重写返回吗
>
> 这时候可以利用服务提供者重写返回

#  自定义分页

## 自定义分页返回处理类

> 这个类放在任何地方都可以，需要继承**Paginator**类  重写里面的**toArray方法**,返回可以自定义为你想要的格式

```php
<?php


namespace app\index\services;


use DomainException;
use think\Paginator;

class ApplySerializePaginate extends Paginator
{

    public function render()
    {
        //  Implement render() method.
    }

    /**
     * 转换为数组
     * @return array
     */
    public function toArray(): array
    {
        try {
            $total = $this->total();
        } catch (DomainException $e) {
            $total = null;
        }

        return [
            'total' => $total,
            'per_page' => $this->listRows(),
            'current_page' => $this->currentPage(),
            'last_page' => $this->lastPage,
            //'data' => $this->items->toArray(),
            'list' => $this->items->toArray(),
        ];
    }
}
```

## 定义服务提供者

> 找到项目目录的**provider**,定义Paginator类指向需要提供服务的类

```php
# 项目目录\app\provider.php
    
<?php

use app\ExceptionHandle;
use app\Request;

// 容器Provider定义文件
return [
    'think\Request' => Request::class,
    'think\exception\Handle' => ExceptionHandle::class,
    \think\Paginator::class => \app\index\services\ApplySerializePaginate::class
];

```

