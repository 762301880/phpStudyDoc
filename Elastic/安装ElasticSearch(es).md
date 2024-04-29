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
| 博客参考         | [link](https://www.jb51.net/article/229894.htm) [link](https://mp.weixin.qq.com/s/QIarKj9ab1CSx0OqFKjrjQ) |

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

