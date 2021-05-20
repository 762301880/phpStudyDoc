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

# 项目实战

> 遇到一个小程序端导出个人填写的`简历信息pdf`功能但是只是成功实现了导出word版本的功能
>
> 解决思路 
>
> * 如下图所示的个人简历word模板 将对应的展示名称修改为`${对应的变量名称}`格式
> * 进行导出，导出结果就不展示了 

![image-20210520093817849](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210520093817849.png)

- 核心代码示例

```php
 public static function getTemplate($hrResume)#$hrResume 传递过来的用户简历的全部参数
    {
        $templateProcessor = new TemplateProcessor(storage_path('word/导出简历模板.docx'));#(加载定义变量的word模板)
        $templateProcessor->setValues(array(
            'realname' => $hrResume['realname'],//姓名
            'gender' => $hrResume['gender'],//性别
            'phone' => $hrResume['phone'],//账号
            'wechat' => $hrResume['wechat'],//微信号
            'email' => $hrResume['email'],//邮箱
            'birth' => $hrResume['birth'],//出生年月
            'grade' => $hrResume['grade'],//年级
            'native_place' => $hrResume['native_place'],//籍贯
            'first_work_date' => $hrResume['first_work_date'],//首次参加工作时间
            'role_text' => $hrResume['role_text'],//身份
            'job_status' => $hrResume['job_status_title'],//求职状态
            'job_city' => $hrResume['job_province'] . '、' . $hrResume['job_city'],//求职省份、求职城市
            'job_industry' => $hrResume['job_industry'] . '、' . $hrResume['job_sub_industry'],//岗位意向:一级、二级
            'job_salary' => $hrResume['job_salary'],//薪酬要求
        ));# setValues统一添加单个变量
     
        # 多个变量统一操作 ?? ""是为了方式变量不存在设置为空要不然无法导出模板
        //教育经历1
        $templateProcessor->setValue('schoolName1', $hrResume['education_experience'][0]['school_name'] ?? "");
        $templateProcessor->setValue('st1', $hrResume['education_experience'][0]['start_date'] ?? "");
        $templateProcessor->setValue('end1', $hrResume['education_experience'][0]['end_date'] ?? "");
        $templateProcessor->setValue('qualification1', $hrResume['education_experience'][0]['qualification'] ?? "");
        $templateProcessor->setValue('major1', $hrResume['education_experience'][0]['major_name'] ?? "");
        //教育经历2
        $templateProcessor->setValue('schoolName2', $hrResume['education_experience'][1]['school_name'] ?? "");
        $templateProcessor->setValue('st2', $hrResume['education_experience'][1]['start_date'] ?? "");
        $templateProcessor->setValue('end2', $hrResume['education_experience'][1]['end_date'] ?? "");
        $templateProcessor->setValue('qualification2', $hrResume['education_experience'][1]['qualification'] ?? "");
        $templateProcessor->setValue('major2', $hrResume['education_experience'][1]['major_name'] ?? "");

        //工作经历1
        $templateProcessor->setValue('company1', $hrResume['work_experience'][0]['company'] ?? "");//公司名称
        $templateProcessor->setValue('workst1', $hrResume['work_experience'][0]['start_date'] ?? "");//起始日期年月
        $templateProcessor->setValue('workendt1', $hrResume['work_experience'][0]['end_date'] ?? "");//截止日期年月
        $templateProcessor->setValue('industry1', $hrResume['work_experience'][0]['industry'] ?? "");//行业
        $templateProcessor->setValue('position1', $hrResume['work_experience'][0]['position'] ?? "");//职位,职务
        $templateProcessor->setValue('work_content1', $hrResume['work_experience'][0]['work_content'] ?? "");//详细内容
        //工作经历2
        $templateProcessor->setValue('company2', $hrResume['work_experience'][1]['company'] ?? "");//公司名称
        $templateProcessor->setValue('workst2', $hrResume['work_experience'][1]['start_date'] ?? "");//起始日期年月
        $templateProcessor->setValue('workendt2', $hrResume['work_experience'][1]['end_date'] ?? "");//截止日期年月
        $templateProcessor->setValue('industry2', $hrResume['work_experience'][1]['industry'] ?? "");//行业
        $templateProcessor->setValue('position2', $hrResume['work_experience'][1]['position'] ?? "");//职位,职务
        $templateProcessor->setValue('work_content2', $hrResume['work_experience'][1]['work_content'] ?? "");//详细内容
        //校园经历1
        $templateProcessor->setValue('club_name', $hrResume['campus_experience'][0]['club_name'] ?? "");//社团名称
        $templateProcessor->setValue('club_category', $hrResume['campus_experience'][0]['club_category'] ?? "");//社团分类
        $templateProcessor->setValue('position', $hrResume['campus_experience'][0]['position'] ?? "");//职位,职务
        $templateProcessor->setValue('club_content', $hrResume['campus_experience'][0]['club_content'] ?? "");//详细描述
        //资格证书1
        $templateProcessor->setValue('type1', $hrResume['qualification_certificate'][0]['type'] ?? "");//证书类型
        $templateProcessor->setValue('title1', $hrResume['qualification_certificate'][0]['title'] ?? "");//证书名称
        //资格证书2
        $templateProcessor->setValue('type2', $hrResume['qualification_certificate'][1]['type'] ?? "");//证书类型
        $templateProcessor->setValue('title2', $hrResume['qualification_certificate'][1]['title'] ?? "");//证书名称
        //保存模板
        $templateProcessor->saveAs("简历模板.docx");# 保存为新的模板(默认保存到public目录下)
        $data = Carbon::now()->toDateTimeString();# 定义当前导出时间戳
        return Response::download("简历模板.docx", "{$data}-简历模板.docx"); # 参数1:模板路径, 参数2:重命名为新的模板
//        //将保存的word转化为pdf
//        Settings::setPdfRendererPath('../vendor/phpoffice/phpword/src/PhpWord/Writer/PDF/DomPDF.php');
//        Settings::setPdfRendererName('DomPDF');
//        $phpWord = IOFactory::load(public_path('/简历模板.docx'));
//        $pdf=IOFactory::createWriter($phpWord,'PDF');
//        $pdf->save('简历模板.pdf');
    }
```

