 

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

> 定义键值对数组 先判断传输过来的只是不是在键值对中然后使用算符判断

```php
$map = [
    0 => '成员',
    1 => '干事',
    2 => '副社长',
    3 => '社长',
];
return $map[$this->position] ?? '游客';
```





