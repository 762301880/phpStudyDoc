## 大白话讲透：为啥有 MySQL 还要 ES？什么时候 MySQL 反而更方便

**最核心结论**：

> MySQL 擅长「精确查找」；Elasticsearch 擅长「**全文模糊搜索 + 分词搜索 + 相关性排序**」。

###  先模拟只用 MySQL 做商城搜索的痛苦

假设你商品表几万条，用户搜索：`oppo手机`

### 方案 1：MySQL like `%oppo手机%`

```sql
select * from goods where title like '%oppo手机%'
```

❌ 致命缺点：

1. **左边带百分号 `%xxx`，索引失效！** 不走索引，全表扫描。商品多了巨卡。
2. **不能分词！** 用户搜 `oppo` → 能搜到； 用户搜 `手机 oppo`，你数据库存的标题是 `oppo手机` `%手机 oppo%` 匹配**不到**。 MySQL 只会做字符串比对，**不会拆词语**。

1. **没有相关度排序** 你希望标题命中关键词排在最前面，简介命中放后面。 MySQL `like` 查出来顺序是随机 / 主键顺序，**不知道哪个结果更匹配用户搜索词**。 你想做权重（标题 > 简介）排序，SQL 写起来又臭又长，性能差。
2. **没有高亮**：搜索结果把关键词标红（🔴oppo 手机），MySQL 查完你还要自己写代码替换字符串。

### 方案 2：MySQL 全文索引 `MATCH AGAINST`

MySQL 自带全文索引，能分词。但是坑超多：

1. **中文支持垃圾**！MySQL 默认不分中文词语。`oppo手机` 它当成一整个字符串，拆不开。想要中文分词还要额外插件，非常麻烦。
2. **相关性算法弱**，排序效果差。
3. **搜索结果不稳定、高级功能几乎没有**。

> 简单一句话：**MySQL 天生就不是为 “用户输入关键词搜东西” 这个场景设计的**。

### ES 到底干了啥魔法（大白话原理）

ES 底层提前把你商品标题用 ik 中文分词器**拆成一个个词语** `oppo手机` → 拆出词语：`oppo`、`手机`

存到一张**倒排索引表**（你就理解成词语‑文档对照表）

| 词语 | 包含这条词的商品 id 列表 |
| ---- | ------------------------ |
| oppo | [1,2,502,501]            |
| 手机 | [502,501,10]             |

当用户搜 `oppo` ES 直接查表：所有包含 `oppo` 的商品，**一瞬间拿出来，不用扫全表**。

并且它会自动：

1. 分词匹配（用户搜 `手机 oppo` 也能命中 `oppo手机`）
2. **计算相关度分数 score**：标题命中分数更高，简介命中分数低，自动按相关性排好序
3. 自带高亮，直接返回给你标红好的数据
4. 轻松实现：标题权重高于简介 `title^3`

这些事情，MySQL 做起来**又慢又难写**，ES 开箱即用。

#### 不是所有场景都要用 ES！MySQL 反而更香

用 MySQL 就够了，千万别上 ES：

1. **后台管理列表搜索（精确搜索）**：根据商品 id、分类 id、状态筛选，不是模糊搜名字。
2. 数据量很小：商品一两千条，`like` 慢不到哪去，没必要额外部署一套 ES 服务，增加维护成本。
3. **增删改高频、查询简单**：只做增删改查、主键查询。
4. 需要强事务：下单、库存扣减。ES **没有事务**，不能当数据库！

> ⚠️ 最重要一条铁律：**MySQL 才是你的真实数据源，ES 永远只是一份 “搜索副本缓存”，不能单独依赖 ES 拿业务数据。**

必须上 ES 的场景（也就是现在商城全局搜索的场景）

1. **前端用户搜索框，关键词模糊检索**（用户随便输入词语搜商品、文章）
2. 需要**中文分词**，搜部分词语就能匹配结果
3. 需要**相关性排序**，越匹配越靠前
4. 需要搜索高亮、权重、后面扩展：同义词、拼写纠错、搜索建议联想词

---

## 通俗类比

| MySQL                 | Elasticsearch                    |
| --------------------- | -------------------------------- |
| 数据库 (database)     | 无直接对应，一般一个项目一批索引 |
| **数据表 (table)**    | **索引 (index)** ✅               |
| 行 (row)              | 文档 (document)                  |
| 列 (column)           | 字段 (field)                     |
| 表结构 (表字段、类型) | mappings（映射）                 |
| sql                   | DSL 查询语句                     |

### ES 索引（index）

**MySQL**

- 一张表，结构固定。建表的时候定义好字段，后续字段类型不能随便改。

```sql
CREATE TABLE goods (
    id int,
    title varchar(255)
)
```

**ES 索引（index）**

你创建索引时写的 `mappings` 就相当于**定义表结构**

```php
'mappings' => [
    'properties' => [
        'title' => ['type'=>'text']
    ]
]
```







---

## [扩展包安装](https://packagist.org/packages/elasticsearch/elasticsearch)

```php
composer require elasticsearch/elasticsearch
```

## env配置

```php
# Elasticsearch
ELASTICSEARCH_HOST=http://127.0.0.1:9200
ELASTICSEARCH_USERNAME=
ELASTICSEARCH_PASSWORD=
```

## config配置elasticsearch.php

```php
<?php
// config/elasticsearch.php
return [
    // ES 服务器地址（默认9200端口，本地启动直接用这个）
    'hosts' => [
        env('ELASTICSEARCH_HOST', 'http://localhost:9200'),
    ],
    // 可选：如果 ES 有账号密码，添加以下配置
    'username' => env('ELASTICSEARCH_USERNAME', ''),
    'password' => env('ELASTICSEARCH_PASSWORD', ''),
    // ES 6.x 注意：默认索引分片数/副本数（可选）
    'settings' => [
        'number_of_shards' => 1,
        'number_of_replicas' => 0,
    ]
];
```



## 单例模式EsService

> 同一个 PHP-FPM 进程生命周期内，**只会执行一次 build () 创建客户端**

```php
<?php


namespace App\Services;
use Elasticsearch\ClientBuilder;


class EsClientService
{
    // 单例模式：避免重复创建客户端连接
    private static $client = null;

    /**
     * 私有构造函数，禁止外部 new
     */
    public function __construct()
    {
    }
    /**
     * 禁止克隆
     */
    private function __clone()
    {

    }
    /**
     * 获取 ES 客户端实例（核心：适配 6.x 版本）
     * @return \Elasticsearch\Client
     */
    public static function getClient()
    {
        if (is_null(self::$client)) {
            // 1. 读取配置文件中的 ES 地址
            $hosts = config('elasticsearch.hosts');
            // 2. 构建客户端构建器
            $builder = ClientBuilder::create()->setHosts($hosts);
            // 3. 如果有账号密码，添加认证（6.x 版本语法）
            $username = config('elasticsearch.username');
            $password = config('elasticsearch.password');
            if (!empty($username) && !empty($password)) {
                #$builder->setBasicAuthentication($username, $password);
            }
            $builder->setRetries(2);// 设置重连次数
            // 4. 创建客户端实例
            self::$client = $builder->build();
        }
        return self::$client;
    }
}
```



## 增删改查代码示例

```php
<?php


namespace App\Http\Controllers;


use App\Services\EsClientService;
use App\Traits\ApplyResponseLayout;
use Illuminate\Http\Request;

/**
 * https://www.bookstack.cn/read/Elasticsearch-PHP-6.0-zh/c232866375290294.md
 * Class EsController
 * @package App\Http\Controllers
 */
class EsController extends Controller
{
    use ApplyResponseLayout;

    // ES 客户端实例
    private $client;

    // 构造函数：初始化 ES 连接
    public function __construct()
    {
        $this->client = EsClientService::getClient();
    }
    // ==========================
    // 【1】创建全局搜索索引（只运行一次）
    // 相当于创建数据库
    //参考 https://learnku.com/docs/elasticsearch-php/6.0/index-operations/2007
    // ==========================
    public function createIndex()
    {

        $params = [
            'index' => 'global_search',  // 索引名称（表名）
            'body' => [
                'settings' => [ // 索引配置（核心设置）
                    'number_of_shards' => 1, // 主分片数量
                    'number_of_replicas' => 0, // 副本分片数量
                ],
                'mappings' => [
                    'properties' => [ //属性
                        'title' => [ // 字段名 => 字段类型
                            'type' => 'text', // 文本（可搜索）
                            'analyzer' => 'ik_max_word' // 中文分词
                        ],
                        'content' => [
                            'type' => 'text', // 文本（可搜索）
                            'analyzer' => 'ik_max_word' // 中文分词
                        ],
                        'type' => [
                            'type' => 'keyword' // 精确值（goods/article）
                        ],
                        'out_id' => [
                            'type' => 'integer' // 精确值（goods/article）
                        ],
                        // ---- 商品额外展示字段（不需要分词，keyword）
                        'cover_image' => [ // 商品封面图
                            'type' => 'keyword'
                        ],
                        'price' => [
                            'type' => 'double'
                        ],
                        'sales' => [
                            'type' => 'integer'
                        ],
                        'category_id' => [
                            'type' => 'integer'
                        ],
                        'category_name' => [
                            'type' => 'keyword'
                        ]
                    ]
                ]
            ]
        ];

        $response = $this->client->indices()->create($params);

        return $this->success('success',$response);


        //打印创建得索引

//        $params = [
//            'index' => 'global_search'
//        ];
//
//        $response = $this->client->indices()->get($params);
//
//        dd($response);
    }

    // ==========================
    // 【2】添加/更新 数据到全局搜索
    // 商品、文章都调用这个方法同步
    // ==========================

    /**
     *
     * {
     * "_index": "global_search",  //文档所在索引名，相当于 MySQL 的「数据库 + 表」，这里数据存在 global_search 索引里。
     * "_type": "_doc",   //文档类型，ES7+ 统一固定为 _doc，废弃了自定义 type，直接忽略即可。
     * "_id": "goods_502", //当前这条文档的唯一主键 ID，手动指定的商品 ID：goods_502。
     * "_version": 1, //文档版本号：首次创建默认 1；后续修改更新，版本会自动累加（ES 乐观锁机制）。
     * "result": "created", //本次操作结果：created：全新文档创建成功 若更新已有文档会返回：updated
     * "_shards": {  //分片集群信息 _shards
     * "total": 1,  //需要同步的总分片数
     * "successful": 1, //写入成功的分片数
     * "failed": 0 //失败分片
     * },
     * "_seq_no": 7,
     * "_primary_term": 1
     * }
     */
    public function addToGlobalSearch(Request $request)
    {
        $type = $request->get('type');
        $id = $request->get('id');
        $title = $request->get('title');
        $content = $request->get('content', '');
        $params = [
            'index' => 'global_search',
            'type' => '_doc',
            'id' => $type . '_' . $id, // 唯一ID：比如 goods_123、article_456
            'body' => [
                'title' => $title,      // 标题
                'content' => $content,  // 简介/内容
                'type' => $type,        // 类型：goods/article/category
                'out_id' => $id,        // 原表ID
            ]
        ];

        // 执行添加/更新
        return $this->client->index($params);
    }


    // ==========================
    // 【3】★ 商城全局搜索（核心方法）
    // 输入关键词 → 返回商品+文章+分类混合结果
    // ==========================
    public function globalSearch(Request $request)
    {
        // 接收搜索词
        $keyword = $request->get('keyword', '');

        // ES 查询参数
        $params = [
            'index' => 'global_search', // 查询全局索引
            'type' => '_doc',
            'body' => [
                'query' => [
                    // 多字段模糊搜索
                    'multi_match' => [
                        'query' => $keyword,         // 搜索关键词
                        'fields' => ['title^3', 'content'], // 标题权重更高
                        'analyzer' => 'ik_max_word' // 中文分词
                    ]
                ],
                'highlight' => [ // 关键词高亮
                    'fields' => [
                        'title' => new \stdClass(),
                        'content' => new \stdClass()
                    ]
                ]
            ]
        ];

        // 执行搜索
        $response = $this->client->search($params);

        // 格式化结果
        $result = [];
        foreach ($response['hits']['hits'] as $item) {
            $result[] = [
                'id' => $item['_source']['out_id'],    // 原表ID
                'type' => $item['_source']['type'],    // 类型 goods/article
                'title' => $item['_source']['title'],  // 标题
                'content' => $item['_source']['content'],
                'score' => $item['_score'], // 匹配分数（越高越相关）
                'highlight' => $item['highlight'] ?? [] // 高亮
            ];
        }

        // 返回给前端
        return response()->json([
            'total' => $response['hits']['total']['value'],
            'data' => $result
        ]);
    }


    /**
     * 商城全局搜索（分页版本）
     * @param Request $request
     * @return \Illuminate\Http\JsonResponse
     */
    public function globalSearchPage(Request $request)
    {
        // 接收搜索词
        $keyword = $request->get('keyword', '');

        // 接收分页参数（给默认值，防止前端不传）
        $page = $request->get('page', 1);    // 当前页
        $size = $request->get('size', 10);   // 每页条数
        $from = ($page - 1) * $size;        // 计算起始位置

        // ES 查询参数 + 分页
        $params = [
            'index' => 'global_search',
            'type' => '_doc',
            'body' => [
                'query' => [
                    'multi_match' => [
                        'query' => $keyword,
                        'fields' => ['title^3', 'content'],
                        'analyzer' => 'ik_max_word'
                    ]
                ],
                'highlight' => [
                    'fields' => [
                        'title' => new \stdClass(),
                        'content' => new \stdClass()
                    ]
                ]
            ],
            // 👇 这两行就是分页核心
            'from' => $from,
            'size' => $size
        ];

        // 执行搜索
        $response = $this->client->search($params);

        // 格式化结果
        $result = [];
        foreach ($response['hits']['hits'] as $item) {
            $result[] = [
                'id' => $item['_source']['out_id'],
                'type' => $item['_source']['type'],
                'title' => $item['_source']['title'],
                'content' => $item['_source']['content'],
                'score' => $item['_score'],
                'highlight' => $item['highlight'] ?? []
            ];
        }

        // 返回带分页信息的数据（前端非常好用）
        return response()->json([
            'total' => $response['hits']['total']['value'], // 总条数
            'page' => (int)$page,                           // 当前页
            'size' => (int)$size,                           // 每页条数
            'total_page' => ceil($response['hits']['total']['value'] / $size), // 总页数
            'data' => $result
        ]);
    }

    // ==========================
    // 【4】删除全局搜索数据
    // ==========================
    public function deleteFromGlobalSearch($type, $id)
    {
        $params = [
            'index' => 'global_search',
            'type' => '_doc',
            'id' => $type . '_' . $id
        ];

        return $this->client->delete($params);
    }


}

```

##  ES当日志系统(待补充不推荐)

## 直观页面效果图

用户搜索：oppo

页面列表渲染：

🔴oppo手机 004

简介：🔴oppo手机 004 款 xxxxxx

点击 → 跳转到商品 502 详情页

🔴oppo手机 003

简介：🔴oppo手机 003 款 xxxxxx

点击 → 跳转到商品 501 详情页