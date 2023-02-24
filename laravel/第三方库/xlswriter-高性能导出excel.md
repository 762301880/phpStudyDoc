#  资料

| name                                                         | url                                                       |
| ------------------------------------------------------------ | --------------------------------------------------------- |
| [xlswriter](https://xlswriter.viest.me/index.html)高性能excel导出扩展 | [xlswriter](https://xlswriter.viest.me/index.html)        |
| xlswriter-中文文档                                           | [link](https://xlswriter-docs.viest.me/zh-cn)             |
| github-第三方参考                                            | [link](https://github.com/yzh0325/xlswrite)               |
| xlswriter-gitee文档                                          | [link](https://gitee.com/viest/php-ext-xlswriter)         |
| 博客参考                                                     | [link](https://mp.weixin.qq.com/s/T1u1Varbiezmo5v_rix-Pw) |

安装ide-提醒工具

```php
composer require viest/php-ext-xlswriter-ide-helper:dev-master
```

# 安装

## windows安装

### 下载**ddl**安装

| 下载名称                         | 下载地址                                                    |
| -------------------------------- | ----------------------------------------------------------- |
| GitHub Release-(github提供的ddl) | [link](https://github.com/viest/php-ext-xlswriter/releases) |
| PECL-(php官网提供的ddl)          | [link](https://pecl.php.net/package/xlswriter)              |
| php中文网-php安装扩展            | [link](https://www.php.cn/php-weizijiaocheng-392756.html)   |

- 我下载的是GitHub提供的ddl

  >php 版本不知道的可以**win+r**输入***cmd*** 输入***php -v***查看对应版本

  <img src="https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210917144950885.png" alt="image-20210917144950885" style="zoom:50%;" />

- 下载之后配置

  > 然后将***php_xlswriter.dll***(我们只需要复制这个)，复制转移到php目录下的**ext目录中**
  >
  > 1. 本人采用的是集成环境所以复制到***D:\phpstudy_pro\Extensions\php\php7.4.3nts\ext***目录下，
  >
  > 2. 然后打开**php.ini**文件 大概在711行加入***extension=xlswriter***
  >
  > 3. 重启阿帕奇 
  > 4. 输入代码phpinfo(),打开看见网站的扩展有`xlswriter`即配置成功

## Alpine安装

- 编辑源

```shell
vi /etc/apk/repositories
# 添加官方 Testing 源
http://nl.alpinelinux.org/alpine/edge/testing
```

- 使用 APK 安装

```shell
apk add php7-pecl-xlswriter
```

# 导出

- 简单导出示例

```php
        $config = [
            //这里设置为导出到项目的public目录下
            'path' => public_path('/') // xlsx文件保存路径
        ];
        $excel = new \Vtiful\Kernel\Excel($config);
        /*
         * fileName 会自动创建一个工作表，
         * 你可以自定义该工作表名称，工作表名称为可选参数
         */
        $filePath = $excel->fileName('tutorial01.xlsx', 'sheet1')
            # 导出的标题
            ->header(['Item', 'Cost'])
            # 导出的数据
            ->data([
                ['Rent', 1000],
                ['Gas', 100],
                ['Food', 300],
                ['Gym', 50],
            ])
            ->output();

# 导出简单实战
    public function test(Request $request)
    {
        $config = [
            //这里设置为导出到项目的public目录下
            'path' => public_path('/') // xlsx文件保存路径
        ];
        $excel = new \Vtiful\Kernel\Excel($config);
        /*
         * fileName 会自动创建一个工作表，
         * 你可以自定义该工作表名称，工作表名称为可选参数
         */
        $data = DB::table('stu')->select(['id','sname'])->get()->map(function ($query) {
            $query->aaa = 123;
            return $query;
        })->toArray();
        $datas=[];
        foreach ($data as $value){
            $datas[]=array_values((array)$value);
        }
        $filePath = $excel->fileName('tutorial01.xlsx', 'sheet1')
            # 导出的标题
            ->header(['Item', 'Cost']);
        # 导出的数据
        $filePath = $filePath->data($datas);
        $filePath = $filePath->output();
        return $filePath;
    }
# 导出实践2  '跟进信息' 是一对多数据 例如导出 跟进人1，跟进时间，跟进信息|跟进人，跟进时间，跟进信息
    public function export($input)
    {
        $config = ['path' => BASE_PATH . '/public/xls/'];
        $excel = new \Vtiful\Kernel\Excel($config);
        $file_name = 'shop.' . date('ymdhis') . '.xlsx';

        $fields = explode(',', ShopImport::$importFields);
        $excel = $excel->fileName($file_name)->header([
            '主键',
            '店铺名称',
            '经营模式',
            '店铺行业分类',
            '店铺行业',
            '联系人名称',
            '手机号码',
            '省',
            '城市',
            '区',
            '店铺地址',
            '经度',
            '纬度',
            '意向度',
            '类型',
            '跟进状态',
            '跟进信息',
        ]);
        $query = DB::table('shop');
        $query = $this->setCondition($query, $input);
        $arr = $query->select($fields)->orderBy('id')->get()->map(function ($query) {
            $query->status = $this->getStatusText($query->status);//修改跟进状态
            $followUps = FollowUp::where('store_id', $query->id)->select('staff_id', 'created_at', 'content')->get();
            $query->info =  $this->getImploadText($followUps);
            return $query;
        })->toArray();
        $export_datas = [];
        foreach ($arr as $value) {
            $export_datas[] = array_values((array)$value);
        }
        $excel = $excel->data($export_datas);
        $excel = $excel->output();
        return $this->path2AssetsUrl($excel);
    }
```

- 简单导入实验

```php
        $config = ['path' => public_path('/')];
        $excel = new \Vtiful\Kernel\Excel($config);
        // 读取测试文件
        // $file=$request->file('file')->getClientOriginalName();
        $data = $excel->openFile('tutorial01.xlsx')
            ->openSheet()
            ->getSheetData();
        dd($data); //返回数据表中的全部数据
```

# 导入实战

- 测试导入excel数据

| name | sex  | age  | phone       |
| ---- | ---- | ---- | ----------- |
| 213  | 男   | 18   | 17538397579 |
| 李四 | 男   | 20   | 15290237819 |
| 芳芳 | 女   | 18   | 13135694568 |
| 小雅 | 女   | 26   | 15698745625 |

- 测试数据库

  ```sql
  CREATE TABLE `user` (
    `id` int(255) NOT NULL AUTO_INCREMENT,
    `name` varchar(255) DEFAULT NULL,
    `sex` varchar(255) DEFAULT NULL,
    `age` int(11) DEFAULT NULL,
    `phone` bigint(255) DEFAULT NULL,
    `created_at` timestamp NULL DEFAULT NULL,
    `updated_at` timestamp NULL DEFAULT NULL,
    PRIMARY KEY (`id`)
  ) ENGINE=InnoDB AUTO_INCREMENT=34 DEFAULT CHARSET=utf8mb4;
  ```

  

- 代码逻辑

```php
# 通过表单上传
/**
 * 本来想着直接上传导入的看来不支持
 * 所以先上传到服务器然后再进行数据处理吧,
 * 如果真实开发环境需要弄一个定时任务每天删除
 * 上传的临时excel文件
 */
    public function test(Request $request)
    {
        $file_name = time() . $request->file('file')->getClientOriginalName();# 设置上传的文件名称为了不重复设置了时间戳
        $date = date('Ymd');
        $path_name = public_path("/upload_temp_excel/{$date}");
        $request->file('file')->move($path_name, $file_name);
        $config = ['path' => $path_name];
        $excel = new \Vtiful\Kernel\Excel($config);
        // 读取测试文件
        if (!file_exists($path_name . '/' . $file_name)) {
            return response()->json(['code' => '5500', 'message' => '文件上传失败', 'data' => []]);
        }
        $data = $excel->openFile($file_name)
            ->openSheet()
            ->setSkipRows(1)
            ->getSheetData();
        $message = $this->importExcel($data);
        unset($excel);# 释放资源(如果不释放w)
        unlink($path_name . '/' . $file_name);//删除上传的临时文件
        return response()->json(['code' => '2000', 'message' => '导入成功', 'data' => $message]);
    }
    /**
     * 导入excel并返回错误信息，行号错误的返回错误信息成功的直接插入数据库
     */
    protected function importExcel(array $data)
    {
        $errorMessage = [];
        foreach ($data as $key => $value) {
            $key = $key + 1;//默认传入的数组下标是第0行所以自增1 第几行就是第几行
            $chinese_bool = preg_match('/[^\u4E00-\u9FA5]/', $value[0]) == 0 ? false : true;//返回0 是中文
            if ($chinese_bool == false) {
                $errorMessage[] = ['num' => "第{$key}行", 'errorMessage' => '姓名只能为中文'];//==0不是中文
            }
            $phone_bool = DB::table('user')->where('phone', $value[3])->exists() == false ? true : false;//返回false手机号不存在
            if ($phone_bool == false) {
                $errorMessage[] = ['num' => "第{$key}行", 'errorMessage' => '手机号码已存在'];
            }
            if ($chinese_bool && $phone_bool) {
                DB::table('user')->insert([
                    'name' => $value[0],
                    'sex' => $value[1],
                    'age' => $value[2],
                    'phone' => $value[3],
                ]);
            }
        }
        return $errorMessage;
    }
```



# 遇到的坑

##   遇到老版本的excel ，**xls**格式无法获取数据

> 目前比较坑的就是作者也说了,只支持xlsx格式坑爹的玩意
>
> 如果有补充后续添加
