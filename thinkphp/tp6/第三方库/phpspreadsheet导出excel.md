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

# 补充

>上述不是说了要做成可以封装的那种(一个函数 三个参数  需要传导出的名称列表，需要导出的数据，需要导出的excel名称)模式这里可以参考一下     https://www.cnblogs.com/laowangbk/p/13321398.html

```php
        $arr = [];
        # 模拟赋值 测试传递名称专用 最大 701
        for ($i = 0; $i <= 701; $i++) {
            $arr[] = "名称" . $i;
        }
        # 模拟赋值字母
//        for ($i = ord('a'); $i <= ord('z'); $i++) {
//            $resData[] = strtoupper(chr($i)) . "$one";
//        }
        for ($i = 0; $i <= count($arr); $i++) {
            $y = ($i / 26);
            if ($y >= 1) {
                $y = intval($y);
                $yCode[] = chr($y + 64) . chr($i - $y * 26 + 65).'1';
            } else {
                $yCode[] = chr($i + 65).'1';
            }

        }
        dd($yCode);


# 返回示例
array:703 [▼
  0 => "A1"
  1 => "B1"
  2 => "C1"
  3 => "D1"
  4 => "E1"
  5 => "F1"
  6 => "G1"
  7 => "H1"
  8 => "I1"
  9 => "J1"
  10 => "K1"
  11 => "L1"
  12 => "M1"
  13 => "N1"
  14 => "O1"
  15 => "P1"
  16 => "Q1"
  17 => "R1"
  18 => "S1"
  19 => "T1"
  20 => "U1"
  21 => "V1"
  22 => "W1"
  23 => "X1"
  24 => "Y1"
  25 => "Z1"
  ....  
```

## [读取excel](https://blog.csdn.net/u010698107/article/details/124775260)

```php
 $excel_file = $request->file('file');
        if (empty($excel_file)) throw new SystemException("需要上传的excel文件不能为空");
        $file_info = $excel_file->getInfo();
        $real_file_name = $file_info['name'] ?? "";
        $real_file_name_arr = explode('.', $real_file_name);
        $real_file_name_prefix = current($real_file_name_arr);//获取上传文件的前缀
        $real_file_suffix = end($real_file_name_arr);//获取上传文件的后缀
        $temp_file_name = $real_file_name_prefix . time() . '' . '.' . $real_file_suffix; //构建临时上传文件名称
        $upload_file_path = "static/import";
        if (!is_dir($upload_file_path)) mkdir($upload_file_path);//目录不存在创建默认目录
        $isUpload = $excel_file->move($upload_file_path, $temp_file_name);//上传临时文件到本地
        if (!$isUpload) throw new SystemException("上传excel文件失败,请重新上传");
        $path_file_name = $upload_file_path . '/' . $temp_file_name; //文件路径+名称
        # 读取excel
        switch ($real_file_suffix) {
            case 'csv'://csv类型
                $reader = new \PhpOffice\PhpSpreadsheet\Reader\Csv();
                $spreadsheet = $reader->load($path_file_name);
                $worksheet = $spreadsheet->getActiveSheet();
// $worksheet   = $spreadsheet->getSheetByName('testcase');
// $rawCasedata = $worksheet->toArray();
                $highestRow = $worksheet->getHighestRow(); // 取得总行数
                $highestColumn = $worksheet->getHighestColumn(); // 取得总列数
                $highestColumnIndex = Coordinate::columnIndexFromString($highestColumn); // 取得总列数

                $excelData = [];
                for ($row = 1; $row <= $highestRow; $row++) {
                    for ($col = 1; $col <= $highestColumnIndex; $col++) {
                        $excelData[$row][] = (string)$worksheet->getCellByColumnAndRow($col, $row)->getValue();
                    }
                }
                dd($excelData);
        }
        return true;
```

