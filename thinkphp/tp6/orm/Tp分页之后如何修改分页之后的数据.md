# 说明

> 采用***$printersModel->getCollection()***获取分页之后的数据，然后采用**map()**
>
>方法修改原有的值

# 逻辑代码示例

```php
    public function index(Request $request)
    {
        //初始化item
        if (input('item') == 'index') {
            $this->setAdminCurItem('index');
        } elseif (input('item') == 'bind') {
            $this->setAdminCurItem('bind');
        } else {
            $this->setAdminCurItem('add');
        }
        //查询实例
        $printersModel = PrintersModel::where(function ($query) use ($request) {
            //查询打印机的类型
            !empty($request->get('printer_type')) && $query->where('printer_type', $request->get('printer_type'));
        })
            ->paginate($request->get('pagesize'));
        # 分页之后修改数据(记住不要再用等于号赋值)
        $printersModel->getCollection()->map(function ($printersModel) {
            $printersModel->printer_title = $this->getPrinterTitle($printersModel->printer_type)??"";//自定义返回打印机名称
        });
        return view('printers/index', compact('printersModel'));
    }
```

