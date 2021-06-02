# 叙述

> 项目写了一个excel导出的功能发现到测试服务器只能导出10条数据，于是看了一下请求的数据
>
> phone=&realname=&role_text=&jobStatus=&job_city=&advice_is_replied=&gender=&job_industry=&grade=&city=&page=1&pageSize=10&total=66
>
> 原来是pageSize=10限定了excel输出的长度，找到问题下面就可以想办法解决

## 解决思路

* 先除却page，pageSize这种必定不为空的请求项，如果其他的请求数据为空则导出全部数据，否则导出请求的查询数据

 ## 核心代码

```php
$inputAll = $this->request->except(['page', 'pageSize','total']);//得到除却页码与每页显示的个数的全部输入项
$inputAll = array_filter($inputAll) == null;//筛选出输入项是否为空,返回boolean类型
```

## 总体代码示例

> excel导出功能不做解释

```php
 public function index($export = null)
    {
        $hrResume = HrResume::filter($this->request->all(), ResumeFilter::class)
            ->paginate($this->request->input('pageSize'));
     /**************************excel导出部分**************************************************/
        if ($export != null) { #如果路由导出不为空
            $inputAll = $this->request->except(['page', 'pageSize','total']);//得到除却页码与每页显示的个数的全部输入项
            $inputAll = array_filter($inputAll) == null;//筛选出输入项是否为空
            if ($inputAll == true) {
                $hrResume = HrResume::all();#如果输入项为空则导出全部的数据
            }
            $date = Carbon::now()->toDateTimeString();# 定义导出时间
            $hrResume = $this->resume->serializeExcelResume($hrResume);# 序列化结果
            return Excel::download(new ResumeExport($hrResume), $date . '简历信息.xlsx'); 
        }
      /**********************excel导出部分*************************************/
        //序列化结果集
        $this->resume->serializeResume($hrResume);
        return $this->success($hrResume);
    }
```