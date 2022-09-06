# laravel自定义验证

# 资料

| 名称                                                         | 地址                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 第三方提供的验证包-40 条附加的 Laravel 表单验证规则   github下载地址 | [link](https://learnku.com/laravel/t/39365)   [link](https://github.com/mattkingshott/axiom) |
|                                                              |                                                              |

自定义文件夹validators

在里面添加自定义验证代码

```php
<?php

namespace App\Services\Admin\Validator;

use App\Http\Models\ClubMember;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Validator;

class UpgradeRoleValidator
{
    public static function verify(Request $request)
    {
        self::customRules($request);#调用自定义验证规则只针对于5.4框架
        return Validator::make($request->all(), self::rules(), self::messages());
    }
   #自定义验证规则
    protected static function rules(): array
    {
        #is_club_member 注意自定义验证规则名称不要用驼峰写法(如果使用的话则无法使用自定义释意)
        return [
            'id' => 'required|integer|is_club_member',//成员表主键
            'position' => 'required|integer',//职位
        ];
    }
#自定义释意
    protected static function messages(): array
    {
        return [
            'id.required' => '成员表id查询条件是必须的',
            'id.integer' => '成员表id查询条件必须是整数',
            'id.is_club_member' => '此成员不存在',
            'position.required' => '职位id条件是必须的',
            'position.integer' => '职位id条件必须是整数',
        ];
    }
# 自定义验证规则
    protected static function customRules(Request $request)
    {
        $user=$request->user(); # 获取当前用户
        # 这里必须要注意 is_club_member 自定义的验证规则名称一定不能为驼峰命名
        Validator::extend('is_club_member', function ($attribute, $value, $parameters, $validator) {
            return ClubMember::find($value) != null;
        });
         # 是否在club模型里 
         Validator::extend('is_in_club', function ($attribute, $value, $parameters, $validator) use($user) {
            return $user::find($value) != null;
        });
        return;
    }
}
```

`调用示例`

```php
        $validator = UpgradeRoleValidator::validate($request);
        if ($validator->fails()) {
            return $this->error($validator->errors()->first());
        }
```

