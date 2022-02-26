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

# 优化序列化为可以导出的数据
 /**
  * 序列化可以优化 将结果序列化对象处理 然后转化为json字符串再转化为数组即可 
  */
  $data=$orderList->getCollection()->map(function ($order){
  return new OrderResponse($order);# OrderResponse
  });
 $data=json_decode(json_encode($data),true);
/**
 * 处理返回结果
 */
<?php
namespace app\admin\Responses;
class OrderResponse
{
    public function __construct($order)
    {
        $this->statistical_date = $order->statistical_date;
        $this->order_num = $order->order_num;
        $this->play_type_count = $order->play_type_count;
        $this->invalid_order_count = $order->invalid_order_count;
    }
}
```

**实战展示2**

> 这里日后可以封装成 一个接口 直接传入需要导出的字段|文件名|数据 即可

```php
 //导出excel数据
    public static function exportExcel($data)
    {
        $date = date('YmdHis');
        $spreadsheet = new Spreadsheet();
        $sheet = $spreadsheet->getActiveSheet();
        // 批量赋值
        $sheet->setCellValue('A1', '门店名称');
        $sheet->setCellValue('B1', '加盟性质');
        $sheet->setCellValue('C1', '省份城市');
        $sheet->setCellValue('D1', '地址');
        $sheet->setCellValue('E1', '开店时间');
        $sheet->setCellValue('F1', '是否在线');
        $sheet->setCellValue('G1', '负责人');
        $sheet->setCellValue('H1', '联系电话');
        # 构建新数据
        if (!empty($data)) {
            $sortData = [];//保存新数据的数组
            foreach ($data as $key=>&$value) {
                unset($value['id'], $value['img_ids']); //删除不需要的字段
                # 构建新字段
                $sortData[$key]['title'] = $value['title'];
                $sortData[$key]['is_nature'] = $value['is_nature'];
                $sortData[$key]['city_path_id'] = $value['city_path_id'];
                $sortData[$key]['address'] = $value['address'];
                $sortData[$key]['start_time'] = $value['start_time'];
                $sortData[$key]['is_online'] = $value['is_online'] == 1 ? '在线' : "不在线";
                $sortData[$key]['principal'] = $value['principal'];
                $sortData[$key]['phone'] = $value['phone'];
            }
        }
        $sheet->fromArray($sortData, null, 'A2');
        $writer = new Xlsx($spreadsheet);
        $exportFileName = '门店导出';
        $directory='public/';
        $savePath= "/static/export/" . "$exportFileName{$date}.xlsx";
        $realPath =$directory.$savePath;
        $saveFullPath = app()->getRootPath() . $realPath;//保存文件的路径
        $writer->save($saveFullPath);
        if (file_exists($saveFullPath)) { //如果文件存在返回文件地址(这里请忽略掉public目录)
            return request()->domain() . '/static/export/' . "$exportFileName{$date}.xlsx";
        }
        return "";
    }
```

# bug记录

##  导出 excel 文件结果集中为0 没有展示

> 解决方案：由于我们的查询结果导出的是**int**类型的**0**，我们将其转化为字符串类型的**"0"**,即可实现导出

```php
# 例如我再处理导出结果的时候如果结果为0转化为字符串类型的"0" 即可实现excel导出结果为0
<?php

namespace app\admin\Responses;


class OrderExcelResponse
{
    public function __construct($orders)
    {
        $this->statistical_date = $orders->statistical_date;
        $this->order_num = $orders->order_num != 0 ? $orders->order_num : '0';
        $this->play_type_count = $orders->play_type_count != 0 ? $orders->play_type_count : '0';
        $this->invalid_order_count = $orders->invalid_order_count != 0 ? $orders->invalid_order_count : '0';
        return $this;
    }
}
```

