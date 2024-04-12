##  解释

> 有时候我们需要自己爬取网站上的接口组装成自己的数据,而从网络上爬取的数据在自己
>
> 的生成环境中不是我们需要的格式`如下图所示`所以我们要进行二次封装

- 示例图片

> 如图所示有很多我们不需要的字段这个时候我们就需要进行二次组装

![image-20210513082946484](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20210513082946484.png)

## 示例代码

```php
   #'services.job_industry_url'= https://www.zhipin.com/wapi/zpCommon/data/position.json 一条爬取的json数据
   $job_industry_url = file_get_contents(Config::get('services.job_industry_url'));#获取数据
        $json = json_decode($job_industry_url, true)['zpData'];//得到需要组转过的数据
        $collect = [];#定义需要抛进数组的集合
        $job = [];# 定义分类集合
        foreach ($json as $value) {#获取一级数据
            $job['name'] = $value['name'];# 定义数据
            foreach ($value['subLevelModelList'] as $item) {#获取二级数据
                $job['subLevelModelList'][] = $item['name'];# 定义数据
            }
            array_push($collect, $job);#将定义的数据抛入集合
        }
        return $this->success($collect);
    }
```

- 示例结果

![image-20210512181516883](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20210512181516883.png)