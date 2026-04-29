##  说明

> 就像mysql一样 我们插入数据 肯定想看一下插入的数据 

## 工具

### Kibana（官方推荐）



### Elasticvue（非常像 Navicat，本人已使用）

这个我觉得更符合你说的“像 Navicat”。

特点：

- web 页面
- 查看 index
- 查看 document
- CRUD
- 看 mapping
- 执行 DSL
- 很轻量

GitHub 上挺多人拿它当 ES 的 GUI。

Docker 启动：

```dockerfile
docker run -d \
-p 8080:8080 \
cars10/elasticvue
```

访问：

```
http://localhost:8080
```

连接你的 ES 即可。

体验比 Kibana 更“数据库管理工具”一点。

####   配置

你必须设置 CORS 才能使用 elasticvue。将以下行添加到你的 **elasticsearch.yml**:

> 配置文件 win在**D:\elasticsearch-7.15.2\config**目录下

```bash
# 允许 CORS 请求来自 http://47.107.33.56:8088
http.cors.enabled: true
http.cors.allow-origin: "http://47.107.33.56:8088"   # elasticvue的公网

# 如果你的集群使用授权:
http.cors.allow-headers: X-Requested-With,Content-Type,Content-Length,Authorization
```

然后重启你的集群.



#### 补充

##### **我是云端安装了Elasticvue**  ES在本地启动

> 方案 因为有服务器可以用**frp** 把本地端口代理到服务器  

