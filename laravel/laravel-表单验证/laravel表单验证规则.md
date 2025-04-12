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

#  控制器方法中使用验证规则(偷懒不使用自定义验证规则情况)

**Validator::make**

> 完全控制验证流程，可以手动创建验证器实例：

```php
        $validator=Validator::make($request->all(),[
            'nick_name' => 'required|string|max:255',
            'email' => 'required|string|email|max:255|unique:users',
            'password' => 'required|string|min:6|confirmed',
        ],[
            'nick_name.required' => '昵称不能为空',
        ]);

       if ($validator->fails()){  
          return $this->error($validator->errors()->first());
       }
        $user = User::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => bcrypt($request->password),
        ]);
        if (!$user) return $this->error('注册失败');
        return $this->success('注册成功');
```

**request->validate**

> https://learnku.com/docs/laravel/8.x/validation/9374#a0ce44
>
> 如果不用**异常接收报错信息** 会重定向到视图模板文件  详细请查询官方文档
>
> 那么，如果传入的请求参数未通过给定的验证规则呢？正如前面所提到的，Laravel 会自动将用户重定向到之前的位置。另外，所有的验证错误信息会自动存储到 闪存 session 中。
>
> 再次提示，我们没有必要在 GET 路由中显式地绑定错误信息到视图中。这是因为 Laravel 会检查 session 数据中的错误，如果可用的话，将会自动将其绑定到视图中去。其中的 $errors 变量是 Illuminate\Support\MessageBag 的一个实例。要获取关于该对象更多的信息， 请参阅这篇文档 。
>
> 技巧：$errors 由 web 中间件组提供的 Illuminate\View\Middleware\ShareErrorsFromSession 中间件绑定到视图中。当该中间件被应用后，$errors 变量在您的视图中总是可用的，因此您可以假设 $errors 变量总是被定义了且总是安全可用的。
>

```php
        try {
             $request->validate([
                'nick_name' => 'required|string|max:255',
                'email' => 'required|string|email|max:255|unique:users',
                'password' => 'required|string|min:6|confirmed',
            ]);
            $user = User::create([
                'name' => $request->name,
                'email' => $request->email,
                'password' => bcrypt($request->password),
            ]);
            if (!$user) return $this->error('注册失败');
            return $this->success('注册成功');
        }catch (ValidationException $exception){
            return $this->error($exception->errors());
        }
```

