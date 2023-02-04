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

**思考:为什么redis端口是6379!(了解一下即可!)**

> https://javajgs.com/archives/75015

**redis是单线程的!**

> 明白redis是很快的,官方表示,redis是基于内存操作,CPU不是redis性能瓶颈,redis的瓶颈是根据机器的内存和网络带宽,既然可以使用单线程
>
> 来实现,就使用单线程了!所以就使用了单线程了!

redis是C语言写的,官方提供的数据为100000+的QPS,完全不比同样使用key-value的Memecache差!

redis为什么单线程还这么快?

> 1. 误区1: 高性能的服务器一定是多线程的?
>
> 2. 误区2: 多线程(CPU上下文会切换!)一定比单线程效率高!
>
>    先去了解  CPU>内存>硬盘 的速度要有所了解!
>
>    核心: redis是将所有的数据全部放在内存中的,所以说使用单线程去操作效率就是最高的,多线程(CPU上下文会切换:耗时的操作!!!!!)
>
>    对于内存系统来说,如果没有上下文切换效率就是最高的!多次读写都是在一个CPU上的,在内存情况下,这个就是最佳的方案!



###  [五大数据类型](https://www.runoob.com/redis/redis-data-types.html)

> Redis是一个开源（BSD许可），内存存储的数据结构服务器，可用作**数据库**，高速**缓存**和**消息队列代理**。它支持[字符串](https://www.redis.net.cn/tutorial/3508.html)、[哈希表](https://www.redis.net.cn/tutorial/3509.html)、[列表](https://www.redis.net.cn/tutorial/3510.html)、[集合](https://www.redis.net.cn/tutorial/3511.html)、[有序集合](https://www.redis.net.cn/tutorial/3512.html)，[位图](https://www.redis.net.cn/tutorial/3508.html)，[hyperloglogs](https://www.redis.net.cn/tutorial/3513.html)等数据类型。内置复制、[Lua脚本](https://www.redis.net.cn/tutorial/3516.html)、LRU收回、[事务](https://www.redis.net.cn/tutorial/3515.html)以及不同级别磁盘持久化功能，同时通过Redis Sentinel提供高可用，通过Redis Cluster提供自动[分区](https://www.redis.net.cn/tutorial/3524.html)。

#### **Redis-Key**

```shell
127.0.0.1:6379> set name zhangsan
OK
127.0.0.1:6379> keys * # 查看所有key
1) "name"
127.0.0.1:6379> set age 23
OK
127.0.0.1:6379> keys *
1) "name"
2) "age"
127.0.0.1:6379> exists name # 判断当前jey是否存在
(integer) 1
127.0.0.1:6379>  move name 1 # 移除namekey
(integer) 1
127.0.0.1:6379> keys *
1) "age"
127.0.0.1:6379> expire age 5 # 设置key过期实践
(integer) 1
127.0.0.1:6379> ttl age # 查看key过期时间
(integer) 2
127.0.0.1:6379> ttl age
(integer) 1
127.0.0.1:6379> ttl age
(integer) -2
127.0.0.1:6379> keys *
(empty array)
127.0.0.1:6379> set name 1
OK
127.0.0.1:6379> type name # 查看key数据类型
string
```



**String(字符串类型)**

> String类似的使用场景:value除了是我们的字符串还可以是我们的数字!
>
> - 计数器
> - 统计多单位数量
> - 粉丝数
> - 对象缓存存储!

```shell
127.0.0.1:6379> keys *
1) "name"
127.0.0.1:6379> exists name # 查询名称是否存在
(integer) 1
127.0.0.1:6379> append name 456 # name key字符串追加456
(integer) 4
127.0.0.1:6379> get name
"1456"
127.0.0.1:6379> strlen name # 查询name key长度
(integer) 4
127.0.0.1:6379> append name  789 # 再次追加
(integer) 7
127.0.0.1:6379> append age 456       # 如果追加啊的key不存在则==新建(set key)
(integer) 3
127.0.0.1:6379> keys * # 查看所有的key 发现多了一个age
1) "name"
2) "age"


#################################################
# 模拟实现阅览量自增命令 类似于编程语言的 i++,i+=指定值
127.0.0.1:6379> set views 0
OK
127.0.0.1:6379> get views
"0"
127.0.0.1:6379> incr views
(integer) 1
127.0.0.1:6379> get views
"1"
127.0.0.1:6379> incr views # 阅览量自增加
(integer) 2
127.0.0.1:6379> get views
"2"
127.0.0.1:6379> decr views # 阅览量自减
(integer) 1
127.0.0.1:6379> get views
"1"
127.0.0.1:6379> incrby views 2      # 阅览量自增2(值可以自定义)
(integer) 3
127.0.0.1:6379> get views
"3"
127.0.0.1:6379> decrby views 2      # 阅览量自减2(值可以自定义)
(integer) 1
127.0.0.1:6379> get views
"1"
#################################################





#################################################
# 字符串范围

127.0.0.1:6379> set key1 "123456789"
OK
127.0.0.1:6379> get key1
"123456789"
127.0.0.1:6379> getrange key1 1 2 # 获取ke1范围第一个下标从0开始
"23"
127.0.0.1:6379> getrange key1 0 1
"12"
127.0.0.1:6379> get key1
"123456789"
127.0.0.1:6379> getrange key1 0  -1     # 查看全部的字符串==get key
"123456789"

# 替换

127.0.0.1:6379> set key2 "abcdefg"
OK
127.0.0.1:6379> get key2
"abcdefg"
127.0.0.1:6379> SETRANGE key2 1 xx  # 替换指定位置开始的字符串，
(integer) 7
127.0.0.1:6379> get key2
"axxdefg"

# setex(set with expire) # 设置过期时间

127.0.0.1:6379> SETEX key3 15 "hello" # 设置key3的值为hello,15秒过期
OK
127.0.0.1:6379> ttl key3
(integer) 11
127.0.0.1:6379> ttl key3
(integer) 2
127.0.0.1:6379> ttl key3
(integer) -2
127.0.0.1:6379> get key3
(nil)
# setnx(set if not exist) # 不存在设置(在分布式锁中常常使用!)

127.0.0.1:6379> SETNX mykey "redis"     # 如果mykey不存在,创建mykey
(integer) 1     # 设置成功返回1
127.0.0.1:6379> keys *
1) "mykey"
2) "key2"
3) "key1"
127.0.0.1:6379> SETNX mykey "mysql"    # 值存在设置失败情况
(integer) 0     # 设置失败返回0
#################################################








#################################################
127.0.0.1:6379> FLUSHDB # 清空数据库
OK
127.0.0.1:6379> keys *     # 查看全部的数据库
(empty array)
# mset 
# mget 
127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3 # 同时设置多个值
OK
127.0.0.1:6379> keys *
1) "k3"
2) "k2"
3) "k1"
127.0.0.1:6379> mget k1 k2 k3  # 同时获取多个值
1) "v1"
2) "v2"
3) "v3"
127.0.0.1:6379> MSETNX k1 v1 k4 v4    # msetnx 是一个原子性的操作,要么一起成功,要么一起失败!
(integer) 0 # 失败返回0
127.0.0.1:6379> get key4
(nil)

# 对象

set user:1 {name:张三,age:3}     # 设置一个user:1 对象 值为 json 字符来保存一个对象!

# 这里的key是一个巧妙的设计: user:{id}:{filed}, 如此设计在redis中是完全ok 

127.0.0.1:6379> MSET user:1:name zhangsan  user:1:age 2
OK
127.0.0.1:6379> mget user:1:name user:1:age
1) "zhangsan"
2) "2"
#################################################
# getset 先get然后再set(先获取一个值再设置一个值)

127.0.0.1:6379> keys *
1) "k2"
2) "user:1:age"
3) "k3"
4) "user:1"
5) "user:1:name"
6) "k1"
127.0.0.1:6379> GETSET db redis # 如果不存在值,则返回nil
(nil)
127.0.0.1:6379> get db   
"redis"
127.0.0.1:6379> GETSET db mysql   # 如果存在值,获取原来的值,并设置新的值
"redis"
127.0.0.1:6379> get db
"mysql"
#################################################
```

#### **List**

> 基本的数据类型,列表 
>
> 在redis里面,我们可以把list完成,栈、队列,阻塞队列!
>
> <font color='red'>所有的list命令都是以l开头的 </font> redis 不区分大小写命令

```shell
127.0.0.1:6379> flushdb # 清空数据库
OK
127.0.0.1:6379> keys *     # 查看所有的key
(empty array)
##########################################################
# 写入list 数据
127.0.0.1:6379> LPUSH list one   # 将一个只或者多个值,插入到列表头部(左)
(integer) 1
127.0.0.1:6379> LPUSH list two
(integer) 2
127.0.0.1:6379> LPUSH list three
(integer) 3
127.0.0.1:6379> get list
# 获取值 最先存的值在最下面
127.0.0.1:6379> LRANGE list 0 -1     # 通过区间获取具体的值!
1) "three"
2) "two"
3) "one"
#########################################################
127.0.0.1:6379> RPUSH list only  # 将一个或者多个值,插入到列表尾部(右)
(integer) 4
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "one"
4) "only"


# 移除一个元素

127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "one"
4) "only"
127.0.0.1:6379> LPOP list       # 移除列表的第一个元素
"three"
127.0.0.1:6379> LRANGE list 0 -1
1) "two"
2) "one"
3) "only"
127.0.0.1:6379> RPOP list  # 移除list的最后一个元素
"only"
127.0.0.1:6379> LRANGE list 0 -1
1) "two"
2) "one"
#########################################################

127.0.0.1:6379> LRANGE list 0 -1
1) "two"
2) "one"
127.0.0.1:6379> LINDEX list 1    # 通过下标获得list中的某一个值!
"one"


#########################################################
127.0.0.1:6379> flushdb
OK
127.0.0.1:6379> LPUSH list one
(integer) 1
127.0.0.1:6379> LPUSH list tow
(integer) 2
127.0.0.1:6379> LPUSH list three
(integer) 3
127.0.0.1:6379> LPUSH list four
(integer) 4
127.0.0.1:6379> LLEN list      #返回列表的长度
(integer) 4

# 移除指定的值!
127.0.0.1:6379> LRANGE list 0 -1
1) "four"
2) "three"
3) "tow"
4) "one"
127.0.0.1:6379> LREM list 1 one     # 移除list集合中指定个数的value,精确匹配
(integer) 1
127.0.0.1:6379> LRANGE list 0 -1
1) "four"
2) "three"
3) "tow"
127.0.0.1:6379> LREM list 1 three
(integer) 1
127.0.0.1:6379> LRANGE list 0 -1
1) "four"
2) "tow"
127.0.0.1:6379> LPUSH list four
(integer) 3
127.0.0.1:6379> LRANGE list 0 -1
1) "four"
2) "four"
3) "tow"
127.0.0.1:6379> LREM list 2 four
(integer) 2
127.0.0.1:6379> LRANGE list 0 -1
1) "tow"

#########################################################

127.0.0.1:6379> flushdb
OK
127.0.0.1:6379> RPUSH mylist "hello"
(integer) 1
127.0.0.1:6379> RPUSH mylist "hello1"
(integer) 2
127.0.0.1:6379> RPUSH mylist "hello2"
(integer) 3
127.0.0.1:6379> RPUSH mylist "hello3"
(integer) 4
127.0.0.1:6379> LRANGE mylist 0 -1
1) "hello"
2) "hello1"
3) "hello2"
4) "hello3"
127.0.0.1:6379> LTRIM mylist 1 2
OK
127.0.0.1:6379> LRANGE mylist 0 -1   # 通过下标街区保留指定区间内的元素。截断了只剩下截取的元素!(类似于修树枝)     
1) "hello1"
2) "hello2"


#########################################################

RPOPLPUSH      # 移除列表的最后一个元素,将他移动到新的列表中!

127.0.0.1:6379> RPUSH mylist "hello1"
(integer) 1
127.0.0.1:6379> RPUSH mylist "hello2"
(integer) 2
127.0.0.1:6379> RPUSH mylist "hello3"
(integer) 3
127.0.0.1:6379> RPUSH mylist "hello4"
(integer) 4
127.0.0.1:6379> LRANGE mylist 0 -1
1) "hello1"
2) "hello2"
3) "hello3"
4) "hello4"
127.0.0.1:6379> RPOPLPUSH mylist myonterlist
"hello4"
127.0.0.1:6379> LRANGE mylist 0 -1     #查看原来的列表
1) "hello1"
2) "hello2"
3) "hello3"
127.0.0.1:6379> LRANGE myonterlist 0 -1     #查看目标的列表中,确实存在该值!
1) "hello4"



#########################################################

# lset 将列表中指定的下标的值替换为另外一个值,更新操作

127.0.0.1:6379> EXISTS list       # 判断这个列表是否存在
(integer) 0
127.0.0.1:6379> LSET list 0 item       # 如果不存在列表我们去更新就会报错
(error) ERR no such key
127.0.0.1:6379> LPUSH list value1
(integer) 1
127.0.0.1:6379> LRANGE list 0 0
1) "value1"
127.0.0.1:6379> LSET list 0 item          # 如果存在,更新当前下标的值
OK
127.0.0.1:6379> LRANGE list 0 0
1) "item"
127.0.0.1:6379> LSET list 1 other      # 如果不存在,则会报错!
(error) ERR index out of range
127.0.0.1:6379> LRANGE list 0 -1
1) "item"

#########################################################
# linsert  将某个具体的value插入到列表某个元素的前面或后面！

127.0.0.1:6379> flushdb
OK
127.0.0.1:6379> RPUSH mylist hello
(integer) 1
127.0.0.1:6379> RPUSH mylist world
(integer) 2
127.0.0.1:6379> LRANGE mylist 0 -1
1) "hello"
2) "world"
127.0.0.1:6379> LINSERT mylist before "world" "other"
(integer) 3
127.0.0.1:6379> LRANGE mylist 0 -1
1) "hello"
2) "other"
3) "world"
127.0.0.1:6379> LINSERT mylist after "world" "new"
(integer) 4
127.0.0.1:6379> LRANGE mylist 0 -1
1) "hello"
2) "other"
3) "world"
4) "new"

```

> **小结**
>
> - 他实际上是一个链表,before node after,left,rigth 都可以插入值
> - 如果key不存在,创建新的链表
> - 如果key存在,新增内容
> - 如果移除了key，所有的值都消失,空链表，也表示不存在
> - 在两边插入或者改动值,效率最高!中间元素,相对来说效率会低一点~

#### [**Set**](https://www.runoob.com/redis/redis-sets.html)

> set中的值是不能重复的!

```shell
127.0.0.1:6379> flushdb
OK
#####################################################################################
127.0.0.1:6379> SADD myset hello     # set 集合中添加唉元素
(integer) 1
127.0.0.1:6379> SADD myset hello      # 再次添加重复元素则会失败
(integer) 0
127.0.0.1:6379> SADD myset hello1
(integer) 1
127.0.0.1:6379> SADD myset hello2
(integer) 1
127.0.0.1:6379> SMEMBERS myset      # 查看指定set的所有值
1) "hello"
2) "hello2"
3) "hello1"
127.0.0.1:6379> SISMEMBER myset hello1 # 判断 hello1 元素是否是集合 key 的成员(判断某一个值是不是在set集合中!)
(integer) 1    # 存在返回1
127.0.0.1:6379> SISMEMBER myset world
(integer) 0    # 不存在返回0

#####################################################################################

127.0.0.1:6379> SCARD myset   # 获取集合的成员数(三个元素)：获取set集合中内容元素的个数
(integer) 3

#####################################################################################
127.0.0.1:6379> SMEMBERS myset      # 查看指定set的所有值
1) "hello"
2) "hello2"
3) "hello1"
127.0.0.1:6379> SREM myset hello     # 移除集合中一个或多个成员
(integer) 1
127.0.0.1:6379> SMEMBERS myset
1) "hello2"
2) "hello1"

#####################################################################################
# set 无序不重复集合,抽随机! 

127.0.0.1:6379> flushdb
OK
127.0.0.1:6379> SADD rand 1
(integer) 1
127.0.0.1:6379> SADD rand 2
(integer) 1
127.0.0.1:6379> SADD rand 3
(integer) 1
127.0.0.1:6379> SADD rand 4
(integer) 1
127.0.0.1:6379> SADD rand 5
(integer) 1
127.0.0.1:6379> SMEMBERS rand
1) "1"
2) "2"
3) "3"
4) "4"
5) "5"
127.0.0.1:6379> SRANDMEMBER rand  # 返回集合中一个随机数
"5"
127.0.0.1:6379> SRANDMEMBER rand 
"3"
127.0.0.1:6379> SRANDMEMBER rand 
"3"
127.0.0.1:6379> SRANDMEMBER rand 
"1"
127.0.0.1:6379> SRANDMEMBER rand 2   # 返回集合中多个随机数
1) "5"
2) "3"

#####################################################################################

# 删除一个指定的key 
## SPOP key 移除并返回集合中的一个随机元素

127.0.0.1:6379> SMEMBERS rand
1) "1"
2) "2"
3) "3"
4) "4"
5) "5"
127.0.0.1:6379> SPOP rand       # 随机删除一些set集合中的元素!
"2"
127.0.0.1:6379> SMEMBERS rand
1) "1"
2) "3"
3) "4"
4) "5"
127.0.0.1:6379> SPOP rand 
"3"
127.0.0.1:6379> SMEMBERS rand
1) "1"
2) "4"
3) "5"

#####################################################################################

# 将一个指定的值,移动到另外一个set集合中

127.0.0.1:6379> flushdb
OK
127.0.0.1:6379> SADD set1 1
(integer) 1
127.0.0.1:6379> SADD set1 2
(integer) 1
127.0.0.1:6379> SADD set1 3
(integer) 1
127.0.0.1:6379> SADD set1 4
(integer) 1
127.0.0.1:6379> SMEMBERS set1     # 查看设置的集合1
1) "1"
2) "2"
3) "3"
4) "4"
127.0.0.1:6379> SADD set2 5 6 7
(integer) 3
127.0.0.1:6379> SMEMBERS set2      # 查看设置的集合2
1) "5"
2) "6"
3) "7"
127.0.0.1:6379> SMOVE set1 set2 4      # 将元素4从set1集合移动到set2集合
(integer) 1
127.0.0.1:6379> SMEMBERS set1
1) "1"
2) "2"
3) "3"
127.0.0.1:6379> SMEMBERS set2
1) "4"
2) "5"
3) "6"
4) "7"

#####################################################################################

# 微博,B站,共同关注!(并集)
# 数字集合类:
  - 差集
  - 交集
  - 并集
  
127.0.0.1:6379> SADD key1 a b c 
(integer) 3
127.0.0.1:6379> SADD key2  c d e
(integer) 3
127.0.0.1:6379> SMEMBERS key1
1) "c"
2) "b"
3) "a"
127.0.0.1:6379> SMEMBERS key2
1) "e"
2) "d"
3) "c"
127.0.0.1:6379> SINTER key1 key2     # 返回给定所有集合的交集(共同好友可以这样实现)
1) "c"
127.0.0.1:6379> SDIFF key1 key2     # 返回第一个集合与其他集合之间的差异。(差集)
1) "a"
2) "b"
127.0.0.1:6379> SUNION key1 key2  #并集
1) "a"
2) "b"
3) "c"
4) "e"
5) "d"

# 案例说明
## 微博,A用户将所有关注的人放在一个set集合中!将它的粉丝也放在一个集合中!
## 共同关注,共同爱好,二度好友,推荐好友
```



#### [**Hash**](https://www.runoob.com/redis/redis-hashes.html) (哈希)

> map集合, key-map集合! 这时候这个值是一个map集合! 本质和String没有太大的区别,还是一个简单的key-value
>
> 对象用hash存储

```shell
127.0.0.1:6379> FLUSHALL
OK
127.0.0.1:6379> HMSET myhash field1 yaoliuyang         # set 一个具体的key-value
OK
127.0.0.1:6379> HMGET myhash field1     # 获取一个字段值
1) "yaoliuyang"
127.0.0.1:6379> HMSET myhash field2 lisi field3 wangwu    #  set多个key-value
OK
127.0.0.1:6379> HMGET myhash field1 field2 field3     # 获取多个字段值
1) "yaoliuyang"
2) "lisi"
3) "wangwu"
127.0.0.1:6379> HGETALL myhash      # 获取在哈希表中指定 key 的所有字段和值
1) "field1"
2) "yaoliuyang"
3) "field2"
4) "lisi"
5) "field3"
6) "wangwu"



# 删除一个值    	HDEL key field1 [field2]      删除一个或多个哈希表字段
127.0.0.1:6379> HMGET myhash field1 field2 field3
1) "yaoliuyang"
2) "lisi"
3) "wangwu"
127.0.0.1:6379> HDEL myhash field3    # 删除hash指定key字段!对应的value值也就消失了!
(integer) 1
127.0.0.1:6379> HGETALL myhash
1) "field1"
2) "yaoliuyang"
3) "field2"
4) "lisi"



# 获取哈希表中字段的数量
127.0.0.1:6379> HGETALL myhash
1) "field1"
2) "yaoliuyang"
3) "field2"
4) "lisi"
127.0.0.1:6379> HLEN myhash      # 获取哈希表中字段的数量
(integer) 2


#  查看哈希表 key 中，指定的字段是否存在 	HEXISTS key field

127.0.0.1:6379> HGETALL myhash
1) "field1"
2) "yaoliuyang"
3) "field2"
4) "lisi"
127.0.0.1:6379> HEXISTS myhash field2 # 查看哈希表 key 中，指定的字段是否存在。
(integer) 1 # 存在返回1
127.0.0.1:6379> HEXISTS myhash field3
(integer) 0  # 不存在返回0


# 只获得的所有的field(key)
127.0.0.1:6379> HKEYS myhash
1) "field1"
2) "field2"
# 只获得所有的value
127.0.0.1:6379> HVALS myhash
1) "yaoliuyang"
2) "lisi"

# 设置自增&自减
127.0.0.1:6379> HSET myhash field3 5   # 初始值设置为5
(integer) 1
127.0.0.1:6379> HINCRBY myhash field3 1 # 自增
(integer) 6
127.0.0.1:6379> HINCRBY myhash field3 1  # 自增
(integer) 7
127.0.0.1:6379> HINCRBY myhash field3 -1  # 自减
(integer) 7


# 只有在字段 field 不存在时，设置哈希表字段的值。
127.0.0.1:6379> HSETNX myhash field4 hello      # 如果不存在则可以设置
(integer) 1
127.0.0.1:6379> HSETNX myhash field4 9           # 如果存在则不能设置
(integer) 0



# hash应用场景  user name age,尤其是用户信息之类的,经常变动的信息! hash更适合于对象的存储,string更适合字符串存储

127.0.0.1:6379> HSET user:1 name yaoliuyang
(integer) 1
127.0.0.1:6379> hget user:1 name
"yaoliuyang"


```

#### **Zset(有序集合)**

> 在set的基础上,增加了一个值，set k1 v1 k2

```shell
127.0.0.1:6379> ZADD myset 1 one    # 添加一个值
(integer) 1
127.0.0.1:6379> ZADD myset 2 two 3 three      # 添加多个值
(integer) 2
127.0.0.1:6379> ZRANGE myset 0 -1       # 查询值
1) "one"
2) "two"
3) "three"
################################################################

# 排序如何实现
## 设置三个人的薪水
127.0.0.1:6379> zadd salary 2500 xiaohong  # 添加三个用户的薪水
(integer) 1
127.0.0.1:6379> zadd salary 5000 zhangsan 
(integer) 1
127.0.0.1:6379> zadd salary 500 yaoliuyang
(integer) 1
127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf # 显示全部的用户,从小到大排序
1) "yaoliuyang"
2) "xiaohong"
3) "zhangsan"
127.0.0.1:6379> ZREVRANGE salary 0 -1     # 从大到小排序
1) "zhangsan"
2) "yaoliuyang"
127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf withscores    # 显示全部的用户,并且附带成绩  
1) "yaoliuyang"
2) "500"
3) "xiaohong"
4) "2500"
5) "zhangsan"
6) "5000"
127.0.0.1:6379> ZRANGEBYSCORE salary -inf 2500 withscores       # 显示工资小于2500员工的降序排列
1) "yaoliuyang"
2) "500"
3) "xiaohong"
4) "2500"


################################################################


# 移除元素

127.0.0.1:6379> ZRANGE salary  0 -1
1) "yaoliuyang"
2) "xiaohong"
3) "zhangsan"
127.0.0.1:6379> ZREM salary xiaohong       # 移除有序集合中的指定元素
(integer) 1
127.0.0.1:6379> ZRANGE salary  0 -1
1) "yaoliuyang"
2) "zhangsan"

# 查看元素
127.0.0.1:6379> ZRANGE salary  0 -1
1) "yaoliuyang"
2) "zhangsan"
127.0.0.1:6379> ZCARD salary       # 获取有序集合中的个数
(integer) 2

# 获取指定区间的成员数量
127.0.0.1:6379> zadd myset 1 hello 2 hello2 3 hello3
(integer) 3
127.0.0.1:6379> ZCOUNT myset 1 3
(integer) 3
127.0.0.1:6379> ZCOUNT myset 1 2     # 获取指定区间的成员数量
(integer) 2


```

> 其余的一些api,通过[官网查询](https://www.redis.net.cn/order/),Redis 有序集合(sorted set) 命令
>
> 案例思路: set 排序  存储班级成绩表,工资表排序!
>
> 普通消息: 1,重要消息  2,带权重进行判断!
>
> 排行榜应用实现，取top N 测试!  

### 三种特殊数据类型

**geospatial**

**hyperloglog**

**bitmaps**







# 扩展补充

### 命令行中如果不记得全部命令可以用**Tab**快捷键自动补全























































































