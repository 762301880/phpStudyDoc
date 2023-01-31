## 什么是NoSQL

> NoSQL=NotOnlySQL(不仅仅是SQL)
>
> 关系型数据库:表格,行,列
>
> 
>
> 泛指非关系型数据库的,随着web2.0互联网的诞生!传统的关系型数据库很难对付web2.0时代!尤其是超大规模的搞并发的社区!
>
> 暴露出来很多难以客服的问题,NoSQL在当今大数据环境下发展的十分迅速，Redis是发展最快的,而且是当下必须要掌握的一门技术！
>
> 很多的数据类型用户的个人信息,社交网络,地理位置。这些数据类型的存储不需要一个固定的格式！不需要多余的操作就可以横向扩展的!

## NoSQL特点

**1.方便扩展(数据之间没有关系,很好扩展!)**

**2.大数据量高性能(Redis一秒钟写8万次,读取11万次,NoSQL的缓存记录,是一种细粒度的缓存,性能会比较高!)**

**3.数据类型是多样型的!(不需要事先设计数据库!随取随用!如果是数据量十分大的表,很多人就无法设计了!)**

**4.传统RDBMS和NoSQL**

> **传统的RDBMS**
>
> - 结构化组织
> - SQL
> - 数据和关系都存在单独的表中
> - 操作,数据定义语言
> - 严格的一致型
> - 基础的事务

> **NoSQL**
>
> - 不仅仅是数据
> - 没有固定的查询语言
> - 键值对存储,列存储，文档存储,图形数据库(社交关系)
> - 最终一致性
> - CAP定理和BASE(异地多活) 初级架构师!
> - 高性能,高可用,高扩展

**了解:3V+3高**

> 大数据时代的3V:主要是描述问题的
>
> - 海量Volume
> - 多样Variety
> - 实时Velocity
>
> 大数据时代的3高:主要是对程序的要求
>
> - 高并发
> - 高可扩(随时水平拆分,机器不够了,可扩展机器)
> - 高性能(保证用户体验和性能!)

真正在公司中的实践:NoSQL+RDBMS一起使用才是最强的,阿里巴巴架构演进!

技术没有高低之分,就看你如何去使用!(提升内功,思维的提高!)

## NoSQL的四大分类

### kv(key:value)键值对:

- 新浪:Redis
- 美团:Redis+Tair
- 阿里、百度:Redis+memecache

### 文档型数据库(bson格式和json一样):

- MongoDB(一般必须要掌握) 

  > MongoDB是一个基于分布式文件存储的数据库,C++编写
  >
  > 主要主力大量的文档!
  >
  > MongoDB是一个介于关系型数据库和非关系型数据库中间的产品!
  >
  > MongoDB是非关系型数据库中功能最丰富,最像关系型数据库的!

- ConthDB

### 列存储数据库

- HBase
- 分布式文件系统

### 图形关系数据库

## Redis入门

### 概述

> Redis是什么?
>
> Redis（**Re**mote **Di**ctionary **S**erver )，即远程字典服务!
>
> 是一个开源的使用ANSI [C语言](https://baike.baidu.com/item/C语言?fromModule=lemma_inlink)编写、支持网络、可基于内存亦可持久化的日志型、Key-Value[数据库](https://baike.baidu.com/item/数据库/103728?fromModule=lemma_inlink)，并提供多种语言的API。
>
> redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。
>
> 免费和开源! 是当下最热门的NoSQL技术之一!也被人们称之为结构化数据库!

> Redis能干嘛?
>
> 1、内存存储(保证效率)、持久化,内存中是断电即失,所以说持久化很重要!(rdb,aof)
>
> 2、效率高,可以用于高速缓存
>
> 3、发布订阅系统
>
> 4、地图信息分析
>
> 5、计时器、计数器(阅览量!)

> 特性
>
> 1、多样的数据类型
>
> 2、持久化
>
> 3、集群
>
> 4、事务

### 测试性能

**redis-benchmark**

> 位于**/usr/local/redis/src/**下,  redis-benchmark是一个压力测试工具!  **此命令可以全局使用**
>
> 官方自带的性能测试工具!
>
> redis-benchmark命令参数!

[redis 性能测试工具可选参数如下所示:](https://www.runoob.com/redis/redis-benchmarks.html)

| 序号 | 选项                      | 描述                                       | 默认值    |
| :--- | :------------------------ | :----------------------------------------- | :-------- |
| 1    | **-h**                    | 指定服务器主机名                           | 127.0.0.1 |
| 2    | **-p**                    | 指定服务器端口                             | 6379      |
| 3    | **-s**                    | 指定服务器 socket                          |           |
| 4    | **-c**                    | 指定并发连接数                             | 50        |
| 5    | **-n**                    | 指定请求数                                 | 10000     |
| 6    | **-d**                    | 以字节的形式指定 SET/GET 值的数据大小      | 2         |
| 7    | **-k**                    | 1=keep alive 0=reconnect                   | 1         |
| 8    | **-r**                    | SET/GET/INCR 使用随机 key, SADD 使用随机值 |           |
| 9    | **-P**                    | 通过管道传输 <numreq> 请求                 | 1         |
| 10   | **-q**                    | 强制退出 redis。仅显示 query/sec 值        |           |
| 11   | **--csv**                 | 以 CSV 格式输出                            |           |
| 12   | ***-l\*（L 的小写字母）** | 生成循环，永久执行测试                     |           |
| 13   | **-t**                    | 仅运行以逗号分隔的测试命令列表。           |           |
| 14   | ***-I\*（i 的大写字母）** | Idle 模式。仅打开 N 个 idle 连接并等待。   |           |

简单测试:

```shell
# 测试:100个并发连接  100000请求
redis-benchmark -h localhost -p 6379 -c 100 -n 100000

....
====== GET ======                                                   
  100000 requests completed in 1.35 seconds   #对我们的10万个请求进行写入测试
  100 parallel clients      # 100个并发客户端
  3 bytes payload  # 每次写入3个字节
  keep alive: 1    # 只有一台服务器来处理这些请求,单机性能
  host configuration "save": 3600 1 300 100 60 10000
  host configuration "appendonly": no
  multi-thread: no
  
Latency by percentile distribution:
0.000% <= 0.167 milliseconds (cumulative count 2)
50.000% <= 0.583 milliseconds (cumulative count 51736)
75.000% <= 0.831 milliseconds (cumulative count 75951)
87.500% <= 0.871 milliseconds (cumulative count 89536)
93.750% <= 0.895 milliseconds (cumulative count 93815)
96.875% <= 0.983 milliseconds (cumulative count 97076)
98.438% <= 1.039 milliseconds (cumulative count 98536)
99.219% <= 1.103 milliseconds (cumulative count 99257)
99.609% <= 1.199 milliseconds (cumulative count 99614)
99.805% <= 1.647 milliseconds (cumulative count 99805)
99.902% <= 1.935 milliseconds (cumulative count 99904)
99.951% <= 2.031 milliseconds (cumulative count 99956)
99.976% <= 2.055 milliseconds (cumulative count 99983)
99.988% <= 2.071 milliseconds (cumulative count 99988)
99.994% <= 2.103 milliseconds (cumulative count 99994)
99.997% <= 2.191 milliseconds (cumulative count 99998)
99.998% <= 2.207 milliseconds (cumulative count 100000)
100.000% <= 2.207 milliseconds (cumulative count 100000) #所有请求在3毫秒内处理完成
.....
```

### 基础的知识

> redis默认有16个数据库,默认使用的是第0个
>
> 可以使用select进行切换数据库!

```bash
# 查看reidis配置中的配置
yaoliuyang@yaoliuyang-PC:/usr/local/redis$ cat redis.conf | grep database

databases 16


# 切换数据库 redis-cli 打开客户端
127.0.0.1:6379> select 3      #切换数据库
OK
127.0.0.1:6379[3]> 

127.0.0.1:6379[3]> DBSIZE #查看当前空间 DB大小
(integer) 0

# 尝试插入一条数据并查看空间大小
127.0.0.1:6379[3]> set name 123       # 数据只会保存在3号数据库(可以用redis可视化工具切换数据库查看)
OK
127.0.0.1:6379[3]> DBSIZE
(integer) 1

127.0.0.1:6379[3]> keys *      # 查看数据库所有的key
1) "name"

# 清空当前库的所有数据
127.0.0.1:6379[3]> flushdb
OK
127.0.0.1:6379[3]> keys *
(empty array)


# 清空所有的数据库中的数据包括当前的数据库
127.0.0.1:6379[3]> flushall     # 此命令大小写都可以
OK

```



































































































