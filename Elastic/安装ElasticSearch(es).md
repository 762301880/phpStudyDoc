[created_at]:2021/12/1
[author]:yaoliuyang

# 参考资料

| 名称                                       | 地址                                                         |
| ------------------------------------------ | ------------------------------------------------------------ |
| 第三方博客                                 | [link](https://learnku.com/articles/49763)                   |
| 阮一峰-全文搜索引擎 Elasticsearch 入门教程 | [link](http://www.ruanyifeng.com/blog/2017/08/elasticsearch.html) |

# windows 安装

[官网下载地址](https://www.elastic.co/cn/downloads/elasticsearch)  [点我直接下载](https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.15.2-windows-x86_64.zip)

##  启动***elasticsearch***

> 打开下载文件中的`\elasticsearch-7.15.2\bin\` 目录 点击 `elasticsearch.bat` 启动

**成功状态**

> 注意**elasticsearch**依赖于**java-jdk**
>
> 浏览器输入**127.0.0.1:9200**出现以下结果代表启动成功

![1638341852(1).jpg](https://i.loli.net/2021/12/01/AghCcSpBPvmz34Z.png)

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

