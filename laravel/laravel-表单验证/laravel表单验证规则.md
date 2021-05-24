# 验证使用

##  [数组验证](https://learnku.com/docs/laravel/8.x/validation/9374#validating-arrays)

> 如有这样一段数据,我需要对`family`家庭中的`sex`性别进行判断只能是整数类型并且只能是0,1

```php
"data":{
    "name":"姚留洋"
     "family":[
         {
            "name":"father",
             "sex":0
         },
         {
             "name":"mather",
             "sex":1
         }
     ]   
}
```

验证逻辑

```php
Validator::make(
                $request['data'],#需要验证的信息
                ['family.*.sex' => 'required|integer|between:0,1'],#验证规则
                [
                    'family.*.sex.required'=>'性别是必须的',
                    'family.*.sex.integer'=>'性别是必须整数',
                    'family.*.sex.between'=>'性别必须是0或1'
                ]#验证逻辑自定义中文
               );
```

