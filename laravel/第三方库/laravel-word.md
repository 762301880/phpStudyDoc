# 说明



## 资料

| packagist(项目地址)   | [链接](https://packagist.org/packages/phpoffice/phpword)     |
| --------------------- | ------------------------------------------------------------ |
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

