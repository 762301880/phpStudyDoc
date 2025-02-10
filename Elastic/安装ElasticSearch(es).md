[created_at]:2021/12/1
[author]:yaoliuyang

# 使用场景

> Elasticsearch是一个**用于实时搜索和分析的分布式存储、搜索、分析引擎**，并且它的实用场景包括**站内搜索、日志实时分析、数据分析、数据监控等**。
>
> Elasticsearch作为一个基于Apache Lucene的开源搜索引擎，它提供了全文搜索功能并能够处理各种类型的数据，比如文本、数字、地理位置以及非结构化数据。其强大的实时性、分布式特性、高性能和易用性使其在众多技术栈中脱颖而出。
>
> 以下是一些具体的使用场景：
>
> 1. **站内搜索**：像电商网站（例如腾讯文档、拼多多、蘑菇街）利用Elasticsearch进行商品搜索，提供快速且相关的搜索结果。
> 2. **日志实时分析**：Elasticsearch支持全栈日志分析，可以处理应用日志、数据库日志、用户行为日志等，实现秒级从采集到展示的能力。
> 3. **数据分析**：时序数据分析是其典型应用场景之一，尤其适用于监控系统数据的实时分析，如云监控等领域。
> 4. **数据监控**：Elasticsearch被用作存储和查询服务，在订单系统中发挥巨大作用，如京东到家订单系统就采用了Elasticsearch作为其核心的数据处理引擎。
> 5. **推荐系统**：在航班信息的数据可视化分析方面，Elasticsearch也可用于支撑推荐系统的后端存储。
> 6. **后端存储**：在某些项目中，可以将Elasticsearch作为主要的后端存储使用，以简化设计并提供持久存储和统计等多项功能。
>
> 总的来说，Elasticsearch以其强大的搜索能力、实时性能、灵活的数据聚合分析和便捷的RESTful API，在现代技术解决方案中占据了重要地位，无论是作为独立搜索引擎还是与其他系统协同工作，都展现出了它的强大实用性。

# 参考资料

| 名称                                       | 地址                                                         |
| ------------------------------------------ | ------------------------------------------------------------ |
| 第三方博客                                 | [link](https://learnku.com/articles/49763)                   |
| 阮一峰-全文搜索引擎 Elasticsearch 入门教程 | [link](http://www.ruanyifeng.com/blog/2017/08/elasticsearch.html) |
| 官方扩展文档                               | [link](https://www.elastic.co/guide/en/elasticsearch/client/php-api/current/index.html) |
| elasticsearch/elasticsearch-packagist      | [link](https://packagist.org/packages/elasticsearch/elasticsearch) |
| elasticsearch中文档(laravel社区)           | [link](https://learnku.com/docs/elasticsearch-php/6.0/quickstart/2001) |

# windows 安装

[官网下载地址](https://www.elastic.co/cn/downloads/elasticsearch)  [点我直接下载](https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.15.2-windows-x86_64.zip)

##  启动***elasticsearch***

> 打开下载文件中的`\elasticsearch-7.15.2\bin\` 目录 点击 `elasticsearch.bat` 启动

**成功状态**

> 注意**elasticsearch**依赖于**java-jdk**
>
> 浏览器输入**127.0.0.1:9200**出现以下结果代表启动成功

![1638341852(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/AghCcSpBPvmz34Z.png)

# 修改配置

## 修改端口

> 找到`\elasticsearch-7.15.2\config`下面的`elasticsearch.yml`文件使用编辑器或者txt打开
>
> 取消**#http.port: 9200** 注释直接修改端口重新启动即可

```shell
# 例如
http.port: 9700
```

默认情况下，Elastic 只允许本机访问，如果需要远程访问，可以修改 Elastic 安装目录的`config/elasticsearch.yml`文件，去掉`network.host`的注释，将它的值改成`0.0.0.0`，然后重新启动 Elastic。

```shell
network.host: 0.0.0.0  #设成0.0.0.0让任何人都可以访问。线上服务不要这样设置，要设成具体的 IP。
```

# docker安装elasticsearch

**说明**

> 推荐使用docker安装这些环境不会把本地的服务搞乱，主要是想怎么搞就怎么搞很是方便

**安装**

> 请在[docker官网下载](https://hub.docker.com/_/elasticsearch)  elasticsearch

```php
docker pull elasticsearch  # 拉取 elasticsearch 镜像

# 启动镜像 更多具体的启动可以查看官网
docker run -itd --name elasticsearch  -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node"  elasticsearch    
    
# 查看是否启动成功(浏览器打开 127.0.0.1:9200) 出现以下数据代表成功
{
  "name" : "0ZZDGyb",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "n1o1hfbRSLWf9W0OSLewZA",
  "version" : {
    "number" : "5.6.12",
    "build_hash" : "cfe3d9f",
    "build_date" : "2018-09-10T20:12:43.732Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}    
```

# [快速开始](https://www.elastic.co/guide/cn/elasticsearch/php/current/_quickstart.html)



**实战资料**

| 名称             | 地址                                                         |
| ---------------- | ------------------------------------------------------------ |
| [第三方博客参考] | [link](https://blog.51cto.com/yszr/2818265)                  |
| 博客参考         | [link](https://www.jb51.net/article/229894.htm) [link](https://mp.weixin.qq.com/s/QIarKj9ab1CSx0OqFKjrjQ)  [link](https://learnku.com/articles/48200) |

## 安装

```php
composer require 'elasticsearch/elasticsearch'
```

## 使用

> 创建ES类

```php
<?php

require 'vendor/autoload.php';

//如果未设置密码
$es = \Elasticsearch\ClientBuilder::create()->setHosts(['xxx.xxx.xxx.xxx'])->build();

//如果es设置了密码
$es = \Elasticsearch\ClientBuilder::create()->setHosts(['http://username:password@xxx.xxx.xxx.xxx:9200'])->build()
```



**写入数据**

```php
        ini_set('max_execution_time', 300);
       foreach ($stus as $stu){
           $params = [
               'index' => 'stu_index',
               'type' => 'stu_type',
               'id' => 'stu_' . $stu['id'],
               'body' => [
                   'id' => $stu['id'],
                   'sname' => $stu['sname'],
                   'class_id' => $stu['class_id'],
                   'birthday' => $stu['birthday'],
                   'updated_at' => $stu['updated_at'],
                   'sex' => $stu['sex'],
                   'created_at' => $stu['created_at'],
               ],
           ];
           $response = $client->index($params);
```

**获取一个文档**

```php

        $params = [
            'index' => 'stu_index',
            'type' => 'stu_type',
            'id' => 'stu_'.'37'
        ];

        $response = $client->get($params);
        print_r($response);
```

**搜索一个文档**

```php
       //搜索
        $serparams = [
            'index' => 'stu_index',
            'type' => 'stu_type',
        ];

        $serparams['body']['query']['match']['sname'] = '钱伊丹';
        $resech = $client->search($serparams);

        dd($resech);

```

## 补充

### 创建索引

> 创建名为 test 的索引 

```php
curl -X PUT http://localhost:9200/test
```

> 服务器返回一个 JSON 对象，里面的 acknowledged 字段表示操作成功。

```shell
{"acknowledged":true,"shards_acknowledged":true,"index":"test"}
```

### 删除索引

> 服务器返回值为，里面的 acknowledged 字段表示操作成功

```shell
curl -X DELETE http://localhost:9200/test

# 返回
{"acknowledged":true}
```

### 查看索引

```shell
curl -X GET http://localhost:9200/_cat/indices?v
```

> 服务器会列出所有索引

```shell
health status index            uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   .geoip_databases if2iRDr1QDGyuBsBZW-rMg   1   0         33            0     31.1mb         31.1mb
yellow open   stu_index        taXvtyClTWqxnTrbHtmQnA   1   1        500            0     85.8kb         85.8kb
yellow open   my_index         Wir5HsZoRR2ytXKWwnEAsQ   1   1          1            0      3.8kb          3.8kb
```



# 可视化工具

## Kibana

> Kibana是Elasticsearch的可视化工具，便于查看和分析数据。
>
> 下载并安装Kibana，可以参考[官方安装指南](https://www.elastic.co/guide/en/kibana/current/install.html)

#  补充知识点

## 项目开发过程中什么时候插入数据呢

> 在项目开发过程中，**何时插入 Elasticsearch 数据**是一个非常重要的问题。插入数据的时机和方式会直接影响系统的性能、数据一致性以及用户体验。以下是几种常见的场景和最佳实践，帮助你决定何时插入 Elasticsearch 数据。

### 1. **数据插入的常见场景**

#### 1.1 **实时插入**

- **场景**：当数据产生后，需要立即被搜索或分析。

- **示例**：

  - 用户发布了一篇文章，希望文章能立即被搜索到。
  - 电商平台新增商品，希望商品能立即出现在搜索结果中。

- **实现方式**：

  - 在业务逻辑中，数据写入数据库的同时，同步插入 Elasticsearch。
  - 例如，用户发布文章时，PHP 代码在将文章存入 MySQL 的同时，也将数据插入 Elasticsearch。

  ```php
  // 示例：用户发布文章时，同步插入 Elasticsearch
  $article = [
      'title' => 'My first article',
      'content' => 'This is the content of the article.',
      'date' => '2023-10-01'
  ];
  
  // 插入 MySQL
  $mysql->insert('articles', $article);
  
  // 插入 Elasticsearch
  $params = [
      'index' => 'articles',
      'body'  => $article
  ];
  $client->index($params);
  ```

  - **优点**：数据实时性高，用户能立即搜索到最新数据。
  - **缺点**：增加了系统复杂性，如果 Elasticsearch 插入失败，可能需要处理回滚或重试逻辑。

  #### 1.2 **异步插入**

  - **场景**：数据不需要立即被搜索，可以容忍一定的延迟。
  - **示例**：
    - 日志数据：日志产生后，可以稍后再插入 Elasticsearch。
    - 批量导入数据：从数据库批量导出数据，异步插入 Elasticsearch。
  - **实现方式**：
    - 使用消息队列（如 RabbitMQ、Kafka）或任务队列（如 Laravel Queue）异步处理数据插入。
    - 例如，用户发布文章时，先将数据存入 MySQL，然后通过消息队列异步插入 Elasticsearch。

```php
// 示例：用户发布文章时，异步插入 Elasticsearch
$article = [
    'title' => 'My first article',
    'content' => 'This is the content of the article.',
    'date' => '2023-10-01'
];

// 插入 MySQL
$mysql->insert('articles', $article);

// 将任务推送到消息队列
$queue->push(new IndexArticleToElasticsearch($article));
```

- **优点**：解耦业务逻辑和 Elasticsearch 插入操作，提高系统性能和可靠性。
- **缺点**：数据有一定的延迟，不适合实时性要求高的场景。

#### 1.3 **定时批量插入**

- **场景**：数据量较大，且不需要实时搜索。

- **示例**：

  - 每天凌晨将前一天的日志数据批量导入 Elasticsearch。
  - 定期将数据库中的历史数据同步到 Elasticsearch。

- **实现方式**：

  - 使用定时任务（如 Cron Job）定期从数据库读取数据，批量插入 Elasticsearch。

  ```php
  // 示例：定时任务批量插入 Elasticsearch
  $articles = $mysql->query('SELECT * FROM articles WHERE created_at > ?', [$lastSyncTime]);
  
  foreach ($articles as $article) {
      $params = [
          'index' => 'articles',
          'body'  => $article
      ];
      $client->index($params);
  }
  ```

  **优点**：减少频繁插入操作，适合大数据量场景。

  **缺点**：数据延迟较大，不适合实时性要求高的场景。

#### 1.4 **数据更新和删除**

- **场景**：当数据库中的数据更新或删除时，Elasticsearch 中的数据也需要同步更新或删除。

- **示例**：

  - 用户修改了文章内容，Elasticsearch 中的文章数据也需要更新。
  - 商品下架后，Elasticsearch 中的商品数据需要删除。

- **实现方式**：

  - 在更新或删除数据库数据时，同步或异步更新 Elasticsearch 中的数据。

  ```php
  // 示例：更新 Elasticsearch 中的数据
  $article = [
      'id' => 1,
      'title' => 'Updated title',
      'content' => 'Updated content.',
      'date' => '2023-10-01'
  ];
  
  // 更新 MySQL
  $mysql->update('articles', $article, ['id' => 1]);
  
  // 更新 Elasticsearch
  $params = [
      'index' => 'articles',
      'id'    => 1,
      'body'  => [
          'doc' => $article
      ]
  ];
  $client->update($params);
  ```



### 2. **如何选择插入时机？**

选择何时插入 Elasticsearch 数据，取决于你的业务需求和系统架构：

- **实时性要求高**：选择实时插入。
- **数据量大，实时性要求低**：选择异步插入或定时批量插入。
- **数据一致性要求高**：确保 Elasticsearch 和数据库的数据同步，可以使用分布式事务或消息队列。

------

### 3. **注意事项**

- **数据一致性**：确保 Elasticsearch 和数据库的数据一致，避免出现数据不同步的情况。
- **性能优化**：批量插入数据时，注意控制每次插入的数据量，避免对 Elasticsearch 造成过大压力。
- **错误处理**：插入 Elasticsearch 时，可能会遇到网络问题或 Elasticsearch 服务不可用的情况，需要设计重试机制或错误处理逻辑。

------

### 4. **总结**

在项目开发中，插入 Elasticsearch 数据的时机取决于业务需求：

- 实时性要求高的场景，选择实时插入。
- 数据量大或实时性要求低的场景，选择异步插入或定时批量插入。
- 确保数据一致性，并合理设计错误处理机制。

如果你有更多具体场景或问题，欢迎继续提问！





