# 说明

> thinkphp是不支持像laravel那种路由前缀写法但是有其他的方法也可以同样实现

## 代码示例

```php
//打印机管理
Route::group(':printer', function () {
    Route::get('list', 'O2oPrinter/index');//打印机列表
    Route::post('store', 'O2oPrinter/store');//添加打印机
    Route::post('delete', 'O2oPrinter/delete');//删除打印机
});
# 调用 你的虚拟域名/模块名/printer/list
# :printer是一个变量你可以随意的指定参数
```

