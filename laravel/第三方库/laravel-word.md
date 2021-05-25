# 说明



## 资料

| packagist(项目地址)   | [链接](https://packagist.org/packages/phpoffice/phpword)     |
| --------------------- | ------------------------------------------------------------ |
| 第三方博客            | [链接](https://segmentfault.com/a/1190000019479817?utm_source=tag-newest)，[链接](https://www.jb51.net/article/162601.htm) |
| 官方手册(英文),(中文) | [链接](https://phpword.readthedocs.io/en/latest/)，[链接](https://phpword-zh.readthedocs.io/zh_CN/latest/) |
| 文档中示例            | [链接](https://github.com/PHPOffice/PHPWord/tree/master/samples/) |

# 安装

- 使用`composer`安装
- 在项目的根目录执行以下命令

```shell
composer require phpoffice/phpword
```



# 使用示例

- 基本用法

```php
use PhpOffice\PhpWord\PhpWord; # 需要使用的类

$phpWord = new PhpWord(); #创建新文档
$section = $phpWord->addSection();# 注意:添加到文档中的任何元素都必须位于Section中,添加一个空的Section到文档
/****************************************中断符*****************************************************/
$section->addText('您好我是第一串内容');#在Section中添加Text元素，默认设置字体样式
/****************************************中断符*****************************************************/
//注意:可以通过三种方式自定义Text元素的字体样式:
//——内联;
// -使用命名字体样式(新的字体样式对象将隐式创建);
//使用显式创建的字体样式对象。
$section->addText('您好我是第二串内容',array('name' => 'MS UI Gothic', 'size' => 20));#参数1:文字 name=设置的字体名称(不知道字体的可以去word文档中查询对应的字体),参数2:size=字体的大小
/****************************************中断符*****************************************************/
//添加文本元素与字体自定义使用命名字体样式…
$fontStyleName = 'oneUserDefinedStyle';#自定义样式,oneUserDefinedStyle=变量说明文字
        $phpWord->addFontStyle(
        $fontStyleName,#定义的变量
        array('name' => 'Tahoma', 'size' => 10, 'color' => 'red', 'bold' => true));#设置的央视
$section->addText('您好我是第三串内容',$fontStyleName);
/****************************************中断符*****************************************************/
//文本元素与字体定制使用显式创建的字体样式对象…
 $fontStyle = new \PhpOffice\PhpWord\Style\Font();#实例化字体类
        $fontStyle->setBold(true);
        $fontStyle->setName('Tahoma');
        $fontStyle->setSize(13);//定义字体的样式
        $myTextElement = $section->addText('您好我是第四串内容');#定义段落内容
        $myTextElement->setFontStyle($fontStyle);#将定义的字体样式加入段落
/****************************************中断符*****************************************************/
//写出文档为word
$objWriter = \PhpOffice\PhpWord\IOFactory::createWriter($phpWord, 'Word2007');#参数1:定义的段落,参数2:需要导出的文档类型名称&版本
$objWriter->save('helloWorld.docx');#保存的文档的名称

//写出文档为odt
// Saving the document as ODF file...
$objWriter = \PhpOffice\PhpWord\IOFactory::createWriter($phpWord, 'ODText');
$objWriter->save('helloWorld.odt');

//写出文档为html
$objWriter = \PhpOffice\PhpWord\IOFactory::createWriter($phpWord, 'HTML');
$objWriter->save('helloWorld.html');
```

#  修改已存在的word[模板](https://phpword-zh.readthedocs.io/zh_CN/latest/templates-processing.html#id2)

> 简单来说就是对已经存在的模板进行变量的修改，保存为新的word文档
>
> 使用场景：假使我们需要做一个简历系统，需要对之前输入的信息进行导出简历，
>
> 这个时候可以对我们需要的模板进行变量修改，既可以得到需要的文档

### 替换简历（word）文件中的[信息](https://phpword-zh.readthedocs.io/zh_CN/latest/templates-processing.html#id2)

- 使用到的简历

![image-20210504172644865](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210504172644865.png)

> 例如我们需要修改word文档中的姓名与性别我们需要定义 变量 `${变量名称}`

```php
use PhpOffice\PhpWord\TemplateProcessor; # 引入需要使用的类

$templateProcessor = new TemplateProcessor('Template.docx');# 打开已经存在的word模板文档
$templateProcessor->setValue('name','姚留洋');# 将定义的变量赋值
$templateProcessor->setValue('sex','男');
$templateProcessor->saveAs('jianli.docx');# 保存为新的模板
```

### [循环插入行](https://phpword-zh.readthedocs.io/zh_CN/latest/templates-processing.html#clonerow) [参考](https://github.com/PHPOffice/PHPWord/blob/master/samples/Sample_07_TemplateCloneRow.php)

- 图片例子

![image-20210504193601092](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210504193601092.png)

```php
$templateProcessor=new TemplateProcessor(public_path('jianli_moban_742693.docx'));
        $schoolInfo=array(
            ['school'=>'杨集乡小','address'=>'杨集乡小学地址'],
            ['school'=>'杨集一中','address'=>'杨集一中地址'],
            ['school'=>'信阳市实验高中','address'=>'固始县秀水公园附近'],
            ['school'=>'郑州财经学院','address'=>'郑州财经学院地址'],
        );# 定义需要插入的值
        $templateProcessor->cloneRow('school',count($schoolInfo));//复制行 参数2 需要复制的条数
        foreach ($schoolInfo as $key=>$value){
            $templateProcessor->setValue('school#'.($key+1),$value['school']);
            $templateProcessor->setValue('address#'.($key+1),$value['address']);
        }
        $templateProcessor->saveAs('jianli.docx');
```

- 结果示例

![image-20210504193847507](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210504193847507.png)

### [替换图片](https://phpword-zh.readthedocs.io/zh_CN/latest/templates-processing.html#setimagevalue)

```php
$templateProcessor->setImageValue('CompanyLogo', 'path/to/company/logo.png');#直接替换图片 参数1:图片变量 参数2：需要替换的图片地址
# 设置参数的
$templateProcessor->setImageValue('UserLogo', array(
    'path' => 'path/to/logo.png',#需要替换的图片地址
    'width' => 100,#设置图片的宽度
    'height' => 100,#设置图片的高度
    'ratio' => false#比率,只使用用于false，-或f以关闭图像的方面的宽高比。默认情况下，模板图像大小用作“容器”大小。
));
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
        $templateProcessor = new TemplateProcessor(public_path('word/导出简历模板.docx'));#(加载定义变量的word模板)
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

