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





# 实战使用示例

```php
        $orderList=# orm查询结果忽略
        $data = [];
        $date = date('YmdHis');
        # 序列化为可以导出的数据
        /**
         * 序列化可以优化 将结果序列化对象处理 然后转化为json字符串再转化为zh 
         * $data=$orderList->getCollection()->map(function ($order){
         *  return new OrderResponse($order);# OrderResponse
         * });
         * $data=json_decode(json_encode($data),true);
         */
        foreach ($orderList->getCollection()->toArray() as $key => $value) {
            $data[$key]['statistical_date'] = $value['statistical_date'];
            $data[$key]['order_num'] = $value['order_num'];
            $data[$key]['play_type_count'] = $value['play_type_count'];
            $data[$key]['invalid_order_count'] = $value['invalid_order_count'];
        }
        # 序列化 
        if ($request->param('export') != null) {
            $spreadsheet = new Spreadsheet();
            $sheet = $spreadsheet->getActiveSheet();
            // 批量赋值
            $sheet->setCellValue('A1', '时间');
            $sheet->setCellValue('B1', '总订单');
            $sheet->setCellValue('C1', '微信支付订单');
            $sheet->setCellValue('D1', '作废订单');
            $sheet->fromArray($data, null, 'A2');
            $writer = new Xlsx($spreadsheet);
            $writer->save(public_path() . 'downloads/excel/' . "订单统计{$date}.xlsx");
            if (file_exists(public_path() . 'downloads/excel/' . "订单统计{$date}.xlsx")) {
                return $request->domain() . '/downloads/excel/' . "订单统计{$date}.xlsx";
            }
        }

```

