# 说明



## 资料

| packagist(项目地址)   | [链接](https://packagist.org/packages/phpoffice/phpword)     |
| --------------------- | ------------------------------------------------------------ |
| 官方手册(英文),(中文) | [链接](https://phpword.readthedocs.io/en/latest/)，[链接](https://phpword-zh.readthedocs.io/zh_CN/latest/) |

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

