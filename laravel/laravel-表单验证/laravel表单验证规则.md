# 验证使用

##  json[数组验证](https://learnku.com/docs/laravel/8.x/validation/9374#validating-arrays)

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

## 数组验证

```php
 /**
  *   传递数据
  *   {
  *	  "phones":[12345646123456,45646]
  *   }
  */
  $validator = Validator::make($request->all(),
            [
                'phones.*' => 'required|min:5|max:11'
            ],
            [
                'phones.*.required' => '手机号是必须的',
                'phones.*.min' => '手机号最小5位',
                'phones.*.max' => '手机号最大11位',
            ]);
        dd($validator->errors()->first());
  # 返回 "手机号最大11位"    
```

## 判断当前需要插入的值必须存在于另一个数据表中的某一列

> 例如这种情况，我们想要插入虚假的中奖记录，所以中奖的奖项名称必须与奖品列表中的表名对应
>
> 所以需要这么一个判断：使用laravel 验证规则中的**[in](https://learnku.com/docs/laravel/8.x/validation/9374#rule-in)**方法查询

**代码示例**

```php
 protected static function rules(): array
    {
        $prize_names=PullNewPrize::pluck('prize_name')->toArray();
        $prize_names=implode(',',$prize_names);
        return [
            '*.3' => "required|in:$prize_names",//奖品名称
        ];
    }
  protected static function messages(): array
    {
        return [
            '*.3.required' => '奖品名称不能为空',
            '*.3.in' => '奖品名称必须要存在于奖品列表中',
        ];
    }
```

