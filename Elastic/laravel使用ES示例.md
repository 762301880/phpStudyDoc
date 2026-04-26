## 扩展包安装

```php
composer require elasticsearch/elasticsearch
```



## 代码示例

```php
<?php


namespace App\Http\Controllers;


use Elasticsearch\ClientBuilder;
use Illuminate\Http\Request;

/**
 * https://www.bookstack.cn/read/Elasticsearch-PHP-6.0-zh/c232866375290294.md
 * Class EsController
 * @package App\Http\Controllers
 */
class EsController extends Controller
{

    // ES 客户端实例
    private $client;

    // 构造函数：初始化 ES 连接
    public function __construct()
    {
        $hosts = [
            '127.0.0.1:9200',         // IP + Port
        ];
        $clientBuilder = ClientBuilder::create();   // Instantiate a new ClientBuilder
        $clientBuilder->setHosts($hosts);           // Set the hosts
        $clientBuilder->setRetries(2);           // 设置重连次数
        $this->client = $clientBuilder->build();
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
                    ]
                ]
            ]
        ];

        $response = $this->client->indices()->create($params);

        return response()->json($response);


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

    {
    "_index": "global_search",  //文档所在索引名，相当于 MySQL 的「数据库 + 表」，这里数据存在 global_search 索引里。
    "_type": "_doc",   //文档类型，ES7+ 统一固定为 _doc，废弃了自定义 type，直接忽略即可。
    "_id": "goods_502", //当前这条文档的唯一主键 ID，手动指定的商品 ID：goods_502。
    "_version": 1, //文档版本号：首次创建默认 1；后续修改更新，版本会自动累加（ES 乐观锁机制）。
    "result": "created", //本次操作结果：created：全新文档创建成功 若更新已有文档会返回：updated
    "_shards": {  //分片集群信息 _shards
    "total": 1,  //需要同步的总分片数
    "successful": 1, //写入成功的分片数
    "failed": 0 //失败分片
    },
    "_seq_no": 7,
    "_primary_term": 1
    }

     */
    public function addToGlobalSearch(Request $request)
    {
        $type = $request->get('type');
        $id = $request->get('id');
        $title = $request->get('title');
        $content = $request->get('content','');
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

