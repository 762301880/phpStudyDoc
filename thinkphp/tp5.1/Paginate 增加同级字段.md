

```php
<?php


namespace app\common\service;


use think\Paginator;

class PaginateService extends Paginator
{
    public function toArray(){
        try {
            $total = $this->total();
        } catch (\DomainException $e) {
            $total = null;
        }

        $res= [
            'total'        => $total,
            'per_page'     => $this->listRows(),
            'current_page' => $this->currentPage(),
            'last_page'    => $this->lastPage,
            'data'         => $this->items->toArray(),
        ];
        if (!empty($this->appendData)){
            foreach ($this->appendData as $key=>$item){
                $res[$key]=$item;
            }
        }
        return $res;
    }

    /**
     * 自定义新增同级数据
     * @param $data
     * @param array $appendData
     * @return mixed
     */
    public static function appendData($data,$appendData=[]){
        if (!empty($appendData)){
            foreach ($appendData as $key=>$value){
               $data->appendData[$key]= $value;
            }
        }
        return $data;
    }

    public function render()
    {
        // TODO: Implement render() method.
    }
}
```



###  config新增配置文件 paginate.php

```php
<?php
//分页配置
return [
    'type'     => \app\common\service\PaginateService::class,
];
```

