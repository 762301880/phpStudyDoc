#  资料



| name                           | url                                                          |
| ------------------------------ | ------------------------------------------------------------ |
| PhpSpreadsheet官方文档         | [link](https://phpspreadsheet.readthedocs.io/en/latest/)     |
| packagist-扩展包地址           | [link](https://packagist.org/packages/phpoffice/phpspreadsheet) |
| laravel学院PhpSpreadsheet-资料 | [link](https://laravelacademy.org/post/19518)                |
| 第三方博客                     | [第三方博客](https://www.e-learn.cn/topic/3761556)           |

# 安装&使用

## 安装

- 使用[composer](https://getcomposer.org/)将 PhpSpreadsheet 安装到您的项目中：

```php
composer require phpoffice/phpspreadsheet
```

- 或者，如果您打算使用它们，也可以下载文档和示例：

```php
composer require phpoffice/phpspreadsheet --prefer-source
```

- 简单入门使用示例

```php
use PhpOffice\PhpSpreadsheet\Spreadsheet;
use PhpOffice\PhpSpreadsheet\Writer\Xlsx;

$spreadsheet = new Spreadsheet();
$sheet = $spreadsheet->getActiveSheet();
$sheet->setCellValue('A1', 'Hello World !');

$writer = new Xlsx($spreadsheet);
return $writer->save('hello world.xlsx'); # 此命令会自动保存在项目目录的public目录下
```

