# 介绍

> 平时的开发工作过程中我们需要对数据进行excel导入导出的工作，这时候我们就
>
> 需要进行开发为了避免造轮子，我们使用这款很start的第三方excel扩展包

## 参考资料

| 资料                                            | 地址                                                         |
| ----------------------------------------------- | ------------------------------------------------------------ |
| 第三方博客                                      | [链接](https://www.cnblogs.com/yaoliuyang/p/12781031.html)   |
| 官方文档                                        | [链接](https://docs.laravel-excel.com/3.1/getting-started/)  |
| laravel学院博客（导出篇）larvel学院博客(导入篇) | [链接](https://learnku.com/articles/32391)，[链接](https://learnku.com/articles/32400) |

# 安装

## composer[安装](https://docs.laravel-excel.com/3.1/getting-started/installation.html)

```shell
composer require maatwebsite/excel
```

##  添加服务提供者

- laravel<5.6以下需要手动注册
- 打开config/app.php,以下位置添加ServiceProvider

```shell
'providers' => [
    /*
     * Package Service Providers...
     */
    Maatwebsite\Excel\ExcelServiceProvider::class,
]
```

## 添加门面

- laravel<5.6以下需要手动注册
- 打开config/app.php,以下位置添加aliases

```shell
'aliases' => [
    ...
    'Excel' => Maatwebsite\Excel\Facades\Excel::class,
]
```

## 发布配置

- 在根目录下运行以下命令，会在config目录下生成config/excel.php配置文件

```shell
php artisan vendor:publish --provider="Maatwebsite\Excel\ExcelServiceProvider" --tag=config
```

# Excel[导出](https://docs.laravel-excel.com/3.1/exports/)

## 创建导出类

- 使用composer创建
- 此命令会在`app/Imports`下生成创建的UsersImport导入类

```shell
php artisan make:export UsersExport --model=User   # --model=你需要导出的模型类
```

## 简单使用导出示例

   -  添加路由

> 在routes/api.php中定义

```php
use App\Http\Controllers\ExcelController;
Route::get('excel_export',[ExcelController::class,'export']);#excel导出
```

- 添加控制器

```php
use App\Exports\UserExport;
use Illuminate\Support\Carbon;
use Maatwebsite\Excel\Facades\Excel;    
//excel导出
    public function export()
    {
        $data=Carbon::now()->toDateTimeString();# 得到当前的时间
        return Excel::download(new UserExport(),$data.'.stu.xlsx');
    }
 # Excel::download()  参数说明
 # new UserExport()   指定创建的导出类(可以携带参数)
 # $data.'.stu.xlsx'  导出的excel表名称
```

- UserExport表

```php
<?php

namespace App\Exports;

use App\Models\Stu;
use Maatwebsite\Excel\Concerns\FromCollection;

class UserExport implements FromCollection
{
    /**
     * @return \Illuminate\Support\Collection
     */
    public function collection()
    {
        //将查询的结果进行导出,可以书写orm查询语句过滤导出结果
        return User::all();
    }
}

```

- 以上步骤完成之后直接请求接口即可
- 在postman或者浏览器中请求接口地址即可导出excel表

```shell
http://127.0.0.1:8000/api/excel_export
```

## 设定表的标题

- 在UserExport类中实现`WithTitle`方法

```php
<?php

namespace App\Exports;

use App\Models\Stu;
use Maatwebsite\Excel\Concerns\FromCollection;
use Maatwebsite\Excel\Concerns\WithTitle;

class UserExport implements FromCollection, WithTitle
{
    /**
     * @return \Illuminate\Support\Collection
     */
    public function collection()
    {
        //
        return User::all();
    }
    # 设定表的标题
    public function title(): string
    {
        // TODO: Implement title() method.
        return '用户表';
    }
}

```

## 设定excel表内容的标题

- 实现接口`WithHeadings`

```php
use Maatwebsite\Excel\Concerns\WithHeadings; 
class UserExport implements WithHeadings
{
public function headings(): array
    {
        // TODO: Implement headings() method.
        return  ['id主键','姓名','班级id','生日','更新时间','性别'];
    }
}  
```

## 设置表[执行自动宽度计算](https://docs.laravel-excel.com/3.1/exports/column-formatting.html#auto-size)

> 及导出表格的时候显示的宽度是自动的

```php
namespace App\Exports;

use Maatwebsite\Excel\Concerns\ShouldAutoSize;
# 只需实现 ShouldAutoSize 方法 即可
class InvoicesExport implements ShouldAutoSize
{
    ...
}
```



#  Excel[导入](https://docs.laravel-excel.com/3.1/imports/)

## 创建导入类

- 如果想使用导出功能必须先创建一个导入类

- `使用composer创建导出类`

```shell
php artisan make:import UsersImport --model=User        # --model=你需要导出的模型类
php artisan make:import UsersImport                     # 如果不需要可以不指定模型
```

- 上述令会在app/Imports下生成创建的UsersImport导入类

## 在控制器中使用

- 在ExcelController中的import类中使用导入功能

```php
//excel导入
    public function import(Request $request)
    {
         return Excel::import(new UserImport(),$request->file('file'));
    }
   # 参数说明
   # new UserImport() 指定你创建的excel导入类 
   # $request->file('file') 指定传输过来的excel文件
```

## 导入使用验证

### 目前找到的一种方案就是直接写在导入集合中

```php
use Illuminate\Support\Facades\Validator;
class StuImport implements ToModel, WithStartRow,ToCollection
{
   public function collection(Collection $collection)
    {
        $validator = Validator::make($collection->toArray(), [
            '0' => 'required|integer'
        ], [
            '0.integer' => '输入结果必须是整数'
        ]);
        foreach ($collection as $row) {
            User::create([
                'name' => $row[0],
            ]);
        }
    }
}
```



# 所遇bug解析

## 导入时间类型读取为数值类型

 参考[资料](https://learnku.com/laravel/t/43832)

> 例如我再excel表那边的时间值是 2021-02-09，打印得到的值却是 43870

```php
use Illuminate\Support\Carbon;
use PhpOffice\PhpSpreadsheet\Shared\Date;
# 解决方案
Carbon::instance(Date::excelToDateTimeObject($row[2]))->format('Y-m-d'),

# 第二种
/**
 * 使用的时候直接调用即可 例如  dd($this->ExcelToTime($row[0])); 打印"2021-12-06"
 */
private function ExcelToTime(int $date)
 {
        $d = 25569;
        $fixationT = 24 * 60 * 60;
        return gmdate('Y-m-d',($date - $d) * $fixationT);
 }
```

##  数据库写入手机号的时候显示位数不足

> 类似于下面这种强转为**string**类型,数据库记得类型修改为**varchar**

```shell
 foreach ($collection as $row) {
            PullNewGainPrize::create([
                'date' => $row[0],//用户昵称
                'mobile' => (string)$row[1],//用户账号
                'nickname' => $row[2],//用户昵称
                'prize_name' => $row[3],//奖品名称
                'pull_new_prize_id' => PullNewPrize::where('prize_name', $row[3])->first()->id ?? "",
                'type' => PullNewGainPrize::TYPE_FALSE,//后台excel导入的假数据
                'headimgurl' => getRandomHeadPortrait() //虚假头像
            ]);
        }
```



# 注意事项

```php
# 导出请求必须是get请求要不然前端会获取到二进制的数据  s
```

