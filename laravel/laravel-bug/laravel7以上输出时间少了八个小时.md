# 讲解

> 这次做项目的时候使用回了laravel8 版本 但是好巧不巧，
>
> 发现不是输出的格式有问题就是输出的时间不准确

# 问题解决方案

## 问题一

- 输出的时间格式没有序列化；如图所示

![image-20210508083638940](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210508083638940.png)

- 解决方案

> 如果采用代码中格式输出的方式很繁琐以后每次写代码的时候我们都需要格式化
>
> 一下时间然后再输出，这个时候我们可以采用laravel提供的[修改器](https://learnku.com/docs/laravel/8.x/eloquent-mutators/9409)统一的格式化输出时间

- 在对应的模型中使用修改器

```php
//不知道修改器如何使用的可以查阅官方文档   
use Carbon\Carbon;    
    public function getCreatedAtAttribute($value)
    {
        # 使用Carbon函数格式化输出时间
        return Carbon::parse($value)->toDateTimeString();
    }

    public function getUpdatedAtAttribute($value)
    {
        return Carbon::parse($value)->toDateTimeString();
    }
```

## 问题二

- 统一`json`输出时间显示少了八个小时

![image-20210508084248181](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210508084248181.png)

- 解决[方案](https://blog.csdn.net/chniccs/article/details/106115438)

- 官方文档[解说](https://www.bookstack.cn/read/laravel-7.x-zh/211146)

![image-20210508084619793](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210508084619793.png)

- 解决方法
- 在对应的模型中使用方法

```php
  protected function serializeDate(DateTimeInterface $date)
    {
        return $date->format(Carbon::now()->toDateTimeString());
    }
```

