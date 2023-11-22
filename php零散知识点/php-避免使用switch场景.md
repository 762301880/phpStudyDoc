 

# php-避免使用switch场景

# 说明 

很多时候我们写代码的时候会遇到一下场景

> $this->position  ==传输过来的int类型的数值
>
> 这样写代码很臃肿不精简

```php
        switch ($this->position) {
            case 0:
                return "成员";
            case 1:
                return "干事";
            case 2:
                return "副社长";
            case 3:
                return "社长";
            default:
                return '游客';
        }
```

#### 使用数组方式优化

`优化方案一`

> 定义键值对数组 先判断传输过来的只是不是在键值对中然后使用算符判断

```php

        $position=[0=>"成员",1=>"干事",2=>"副社长",3=>"社长",'default'=>'游客'];
        return in_array($this->position,array_keys($position))?$position[$this->position]:$position['default'];

```

`优化方案二`

> 直接写死对应的关系，然后使用下标的方式进行三木运算判断

```php
        $position=["成员","干事","副社长","社长",'游客'];
        return $this->position <= count($position) -1 ?  $position[$this->position] :'游客';
```

`优化方法三`

> 直接匹配如果不存在的话直接返回默认值

```php
$jobStatusTitle=[1 => '就读', 2 => '实习找工作', 3 => "实习有工作", 4 => '毕业找工作', 5 => '毕业有工作','default'=>'暂无工作'];
return $jobStatusTitle[$value]??$jobStatusTitle['default'];
```



