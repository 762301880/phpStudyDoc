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

