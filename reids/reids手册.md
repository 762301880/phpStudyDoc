**资料**

https://blog.csdn.net/wudidahuanggua/article/details/125356361

## 什么是NoSQL

> NoSQL=NotOnlySQL(不仅仅是SQL)
>
> 关系型数据库:表格,行,列
>
> 
>
> 泛指非关系型数据库的,随着web2.0互联网的诞生!传统的关系型数据库很难对付web2.0时代!尤其是超大规模的搞并发的社区!
>
> 暴露出来很多难以克服的问题,NoSQL在当今大数据环境下发展的十分迅速，Redis是发展最快的,而且是当下必须要掌握的一门技术！
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

####  **geospatial(地理位置)**

> 朋友的定位,附近的人,打车距离计算?
>
> Redis的Geo 在Redis3.2版本就已经推出了！ 这个功能可以推算地理位置的信息,两地之间的距离,方圆几里的人!
>
> 可以查询一些测试数据: http://www.jsons.cn/lngcode/
>
> 只有六个命令
>
> #### Redis 地理位置(geo) 命令
>
> | 命令                                                         | 描述                                                      |
> | :----------------------------------------------------------- | :-------------------------------------------------------- |
> | [Redis GEOHASH 命令](https://www.redis.net.cn/order/3687.html) | 返回一个或多个位置元素的 Geohash 表示                     |
> | [Redis GEOPOS 命令](https://www.redis.net.cn/order/3688.html) | 从key里返回所有给定位置元素的位置（经度和纬度）           |
> | [Redis GEODIST 命令](https://www.redis.net.cn/order/3686.html) | 返回两个给定位置之间的距离                                |
> | [Redis GEORADIUS 命令](https://www.redis.net.cn/order/3689.html) | 以给定的经纬度为中心， 找出某一半径内的元素               |
> | [Redis GEOADD 命令](https://www.redis.net.cn/order/3685.html) | 将指定的地理空间位置（纬度、经度、名称）添加到指定的key中 |
> | [Redis GEORADIUSBYMEMBER 命令](https://www.redis.net.cn/order/3690.html) | 找出位于指定范围内的元素，中心点是由给定的位置元素决定    |

```shell
###############################
##  geoadd 添加地理位置       https://www.redis.net.cn/order/3685.html
## 规则,两级无法直接添加,我们一般会下载城市数据,直接通过java程序一次性导入!
## 参数 key 值 (经度,纬度,名称)

我附近的人?(获得所有附近的人的地址,定位!) 通过半径来查询!
获得指定数量的人,200
所有数据因该都录入: china:city,才会让结果更加清晰

127.0.0.1:6379> FLUSHALL
OK

# 添加城市
127.0.0.1:6379> GEOADD china:city 116.405285 39.904989 beijin
(integer) 1
127.0.0.1:6379> GEOADD china:city 121.472644 31.231706 shanghai
(integer) 1
127.0.0.1:6379> GEOADD china:city 106.504962 29.533155 chongqing 
(integer) 1

#   获得当前定位:一定是一个坐标值!

127.0.0.1:6379> GEOPOS china:city beijin        # 获取指定的城市的经度和纬度!
1) 1) "116.40528291463851929"
   2) "39.9049884229125027"

127.0.0.1:6379> GEOPOS china:city beijin chongqing
1) 1) "116.40528291463851929"
   2) "39.9049884229125027"
2) 1) "106.50495976209640503"
   2) "29.53315530684997015"


# 两人之间的距离    
## 单位:
- m 表示单位为米。
- km 表示单位为千米。
- mi 表示单位为英里。
- ft 表示单位为英尺。

127.0.0.1:6379> GEODIST china:city beijin shanghai km     # 查看上海到北京的直线距离
"1067.5980"
127.0.0.1:6379> GEODIST china:city beijin chongqing km     # 查看重庆到北京的直线距离
"1464.2210"


## 我附近的人?(获得所有附近的人的地址,定位!) 通过半径来查询         GEORADIUS

127.0.0.1:6379> GEORADIUS china:city 110 30 1000 km withdist   # 获取110 30 这个经纬度为中心.寻找方圆1000KM的城市,显示到中间距离的位置
1) 1) "chongqing" 
   2) "341.4052"

127.0.0.1:6379> GEORADIUS china:city 110 30 1000 km withcoord   # 显示他人的定位信息   
1) 1) "chongqing"
   2) 1) "106.50495976209640503"
      2) "29.53315530684997015"

127.0.0.1:6379> GEORADIUS china:city 110 30 1000 km withdist withcoord count 2     #  筛选出指定的结果
1) 1) "chongqing"
   2) "341.4052"
   3) 1) "106.50495976209640503"
      2) "29.53315530684997015"
      
      
# 找出位于指定范围内的元素，中心点是由给定的位置元素决定      GEORADIUSBYMEMBER
## 找出位于指定元素周围的其他元素!
127.0.0.1:6379> GEORADIUSBYMEMBER china:city beijin 1100 km
1) "beijin"
2) "shanghai"

#  返回一个或多个位置元素的 Geohash 表示
## 改命令返回11个字符串的Geohash字符串!
## 将二维的经纬度转化为一维的字符串,如果两个字符串越接近,那么则距离越近!
127.0.0.1:6379> GEOHASH china:city beijin chongqing
1) "wx4g0b7xrt0"
2) "wm78p86e170"
```

> GEO底层的实现原理其实就是Zset !  我们可以使用Zset命令来操作geo!

```shell
127.0.0.1:6379> ZRANGE china:city 0 -1      # 查看地图中所有的元素
1) "chongqing"
2) "shanghai"
3) "beijin"
127.0.0.1:6379> ZREM china:city beijin # 移除地图中北京元素
(integer) 1
127.0.0.1:6379> ZRANGE china:city 0 -1
1) "chongqing"
2) "shanghai"

```



####  [**hyperloglog**](https://www.runoob.com/redis/redis-hyperloglog.html)

> 什么是基数?
>
> 基数(不重复的元素)
>
> 两个数据集
>
> A{1,3,5,7,8,9,7}        B{1,2,5,7,8}
>
> 简介:Redis2.8.9版本就更新了Hyperloglog数据结构!
>
> Redis Hyperloglog 基数统计的算法
>
> 网页的UV (一个人访问一个网站多次,但是还是算作一个人!)
>
> 传统的方式,set保存用户的id,然后就可以统计set中的元素数量作为标准判断!(这个方式如果保存大量的用户id,就会
>
> 比较麻烦!我们的用户是为了计数,而不是保存用户id;)
>
> 0.81%错误率!统计UV任务,可以忽略不计的!
>
> **Hyperloglog 优点**
>
> - 占用的内存是固定
> - 2^64不同的元素的技术,只需要废12KB内存!如果要从内存角度来比较的话Hyperloglog首选!
>
> ## Redis HyperLogLog 命令
>
> 下表列出了 redis HyperLogLog 的基本命令：
>
> | 序号 | 命令及描述                                                   |
> | :--- | :----------------------------------------------------------- |
> | 1    | [PFADD key element [element ...\]](https://www.runoob.com/redis/hyperloglog-pfadd.html) 添加指定元素到 HyperLogLog 中。 |
> | 2    | [PFCOUNT key [key ...\]](https://www.runoob.com/redis/hyperloglog-pfcount.html) 返回给定 HyperLogLog 的基数估算值。 |
> | 3    | [PFMERGE destkey sourcekey [sourcekey ...\]](https://www.runoob.com/redis/hyperloglog-pfmerge.html) 将多个 HyperLogLog 合并为一个 HyperLogLog |

```shell
127.0.0.1:6379> FLUSHALL
OK
# 测试使用
127.0.0.1:6379> PFADD mykey a b c d e f g h i j   # 创建第一组元素mykey
(integer) 1
127.0.0.1:6379> PFCOUNT mykey       # 统计mykey元素中的基数数量
(integer) 10    
127.0.0.1:6379> PFADD mykey2 i j z x c v b n m        # 创建第二组元素mykey2       
(integer) 1
127.0.0.1:6379> PFCOUNT mykey2
(integer) 9

127.0.0.1:6379> PFMERGE mykey3 mykey mykey2      # 合并两组mykey mykey2 到 mykey3 并集
OK
127.0.0.1:6379> PFCOUNT mykey3       #  查看并集的数量
(integer) 15

# 如果允许容错,那么一定可以使用Hyperloglog
# 如果不允许容错,就使用set或者自己的数据类型即可!
```

#### Bitmaps

> 位存储
>
> 场景
>
> 统计疫情感染人数: 0 1 0 1 0
>
> 统计用户信息,活跃,不活跃!  登录、未登录!   打卡, 365打卡!     两个状态的,都可以使用Bitmaps
>
> Bitmaps 位图,数据结构! 都是操作二进制来进行记录,就只有0和1两个状态!
>
> 352天=365bit   1字节=8bit       46个字节左右!

```shell
# 测试 使用bitmap来记录周一到周日的打卡! 第一位的0-6 代表周一到周日，第二位的0&1 代表0未打卡 1已打卡             

127.0.0.1:6379> SETBIT sign  0 1  # 周一:1
(integer) 0
127.0.0.1:6379> SETBIT sign  1 0  # 周二:0
(integer) 0
127.0.0.1:6379> SETBIT sign  2 0  # 周三:0
(integer) 0
127.0.0.1:6379> SETBIT sign  3 1  # 周四:1 
(integer) 0
127.0.0.1:6379> SETBIT sign  4 1  # 周五:1
(integer) 0
127.0.0.1:6379> SETBIT sign  5 0  # 周六:0
(integer) 0
127.0.0.1:6379> SETBIT sign  6 0  # 周日:0 
(integer) 0


## 查看某一天是否有打卡!
127.0.0.1:6379> GETBIT sign 4      # 查看周五是否打卡
(integer) 1 
127.0.0.1:6379> GETBIT sign 3      # 查看周四是否打卡 
(integer) 1
127.0.0.1:6379> GETBIT sign 6      # 查看周日是否打卡 
(integer) 0

# 统计操作,统计打卡的天数!

127.0.0.1:6379> BITCOUNT sign      # 统计这周的打卡记录,就可以是否有全勤
(integer) 3     # 一周打卡了三次
```

### [事务](https://www.redis.net.cn/tutorial/3515.html)

> Redis单条命令是保证原子性的!但是事务不保证原子性 ！     
>
> Redis事务本质:一组命令的集合! 一个事务中的所有命令都会被序列化,在事务执行的过程中,会按照顺序执行!
>
> 一次性,顺序性,排他性!执行一系列的命令!
>
> ```shell
> --------- # 队列
> set
> set
> set
> ---------
> ```
>
> Redis事务没有隔离级别的概念！
>
> 所有的命令在事务中,并没有直接被执行!只有发起执行命令的时候才会执行
>
> 什么是事务?  要么同时成功,要么同时失败,原子性!
>
> Redis的事务
>
> - 开启事务(MULTI)
> - 命令入队(...)
> - 执行事务(EXEC)

**Redis 事务命令**

下表列出了 redis 事务的相关命令：

| 序号 | 命令及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | [DISCARD](https://www.redis.net.cn/order/3638.html) 取消事务，放弃执行事务块内的所有命令。 |
| 2    | [EXEC](https://www.redis.net.cn/order/3639.html) 执行所有事务块内的命令。 |
| 3    | [MULTI](https://www.redis.net.cn/order/3640.html) 标记一个事务块的开始。 |
| 4    | [UNWATCH](https://www.redis.net.cn/order/3641.html) 取消 WATCH 命令对所有 key 的监视。 |
| 5    | [WATCH key [key ...\]](https://www.redis.net.cn/order/3642.html) 监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断。 |

```shell
# 正常执行事务
127.0.0.1:6379> MULTI        # 开启事务
OK
# 命令入队
127.0.0.1:6379(TX)> set k1 v1
QUEUED
127.0.0.1:6379(TX)> set k2 v2
QUEUED
127.0.0.1:6379(TX)> get k2 
QUEUED
127.0.0.1:6379(TX)> set k3 v3       
QUEUED
127.0.0.1:6379(TX)> EXEC      # 执行事务
1) OK
2) OK
3) "v2"
4) OK
```

```shell
# 放弃事务
127.0.0.1:6379> MULTI      # 开启事务
OK
127.0.0.1:6379(TX)> set a 1
QUEUED
127.0.0.1:6379(TX)> set b 2
QUEUED
127.0.0.1:6379(TX)> set c 3
QUEUED
127.0.0.1:6379(TX)> DISCARD  # 取消事务
OK
127.0.0.1:6379> get c      # 事务队列中的命令都不会被执行
(nil)
```

> java常见异常
>
> 编译型异常(代码有问题! 命令有错 !),事务中所有的命令都不会被执行
>
> ```shell
> 127.0.0.1:6379> MULTI
> OK
> 127.0.0.1:6379(TX)> set k1 v1 
> QUEUED
> 127.0.0.1:6379(TX)> set k2 v2
> QUEUED
> 127.0.0.1:6379(TX)> set k3 v3
> QUEUED
> 127.0.0.1:6379(TX)> GETSET k3     # 错误的命令
> (error) ERR wrong number of arguments for 'getset' command
> 127.0.0.1:6379(TX)> set k4 v4
> QUEUED
> 127.0.0.1:6379(TX)> set k5 v5
> QUEUED
> 127.0.0.1:6379(TX)> EXEC      # 执行事务报错
> (error) EXECABORT Transaction discarded because of previous errors.
> 127.0.0.1:6379> get k5        # 所有的命令都不会被执行
> (nil)
> ```
>
> 运行时异常(1/0),如果事务队列中存在语法性,那么执行命令的时候,其他命令可以正常执行的,错误命令会抛出异常!
>
> ```shell
> 127.0.0.1:6379> set k1 v1
> OK
> 127.0.0.1:6379> MULTI
> OK
> 127.0.0.1:6379(TX)> INCR k1     # 会执行的时候失败
> QUEUED
> 127.0.0.1:6379(TX)> set k2 v2
> QUEUED
> 127.0.0.1:6379(TX)> set k3 v3
> QUEUED
> 127.0.0.1:6379(TX)> get k3
> QUEUED
> 127.0.0.1:6379(TX)> EXEC
> 1) (error) ERR value is not an integer or out of range   #虽然第一条命令报错了,但是依旧执行成功了
> 2) OK
> 3) OK
> 4) "v3"
> ```
>
> 

### 监控(watch)

> 悲观锁:
>
> - 很悲观,什么时候都会出问题,无论做什么都会加锁!
>
> 乐观锁:
>
> - 很乐观,认为什么时候都不会出现问题,所以不会上锁! 更新数据的时候去判断一下,在此期间是否有人修改过这个数据
> - 获取version
> - 更新的时候比较version

```shell
# Redis监视测试
## 正常执行成功
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0 
OK
127.0.0.1:6379> WATCH money  # 监视 money 对象
OK
127.0.0.1:6379> MULTI  # 事务正常结束,数据期间没有发生变动,这个时候就正常执行成功!
OK
127.0.0.1:6379(TX)> DECRBY money 20
QUEUED
127.0.0.1:6379(TX)> INCRBY out 20
QUEUED
127.0.0.1:6379(TX)> EXEC
1) (integer) 80
2) (integer) 20

```

**测试多线程修改值,使用watch可以当作redis的乐观锁操作!**

```shell
127.0.0.1:6379> WATCH money    # 监控金额 80
OK
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> DECRBY money 10
QUEUED
127.0.0.1:6379(TX)> INCRBY out 10
QUEUED
127.0.0.1:6379(TX)> EXEC     # 执行之前,另外一个线程修改了我们的值,这个时候,就会导致事务执行失败
(nil)

# 线程二
127.0.0.1:6379> get money
"80"
127.0.0.1:6379> set money 1000      # 在上一个执行exec之前修改
OK

```

> 如果修改失败获取最新的值就好

```shell
127.0.0.1:6379> UNWATCH      # 1.如果发现事务执行失败,就先解锁
OK
127.0.0.1:6379> WATCH money    # 2.获取最新的值,再次监视, select version
OK
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> DECRBY money 1
QUEUED
127.0.0.1:6379(TX)> INCRBY money 1
QUEUED
127.0.0.1:6379(TX)> exec     # 3.比对监视的值是否发生了变化,如果没有发生变化,那么可以执行成功,如果变了就执行失败!
1) (integer) 989
2) (integer) 990
```



### [Redis.conf详解](https://www.runoob.com/redis/redis-conf.html)

> 启动的时候,就通过配置文件来启动!
>
> 配置文件unit单位对大小写不敏感
>
> **常用配置讲解**
>
> ```shell
> bind 127.0.0.1       # 指定绑定的本机地址 如果需要开启远程访问可以使用通配符*,或者公网ip地址
> protected-mode yes   # 保护模式
> port 6379            # 端口设置
> ```
>
> **通用配置详解**
>
> ```shell
> daemonize no     # Redis 默认不是以守护进程的方式运行，可以通过该配置项修改，使用 yes 启用守护进程（Windows 不支持守护线程的配置为 no）
> 
> pidfile /var/run/redis_6379.pid    # 如果以后台的方式运行,我们就需要指定一个pid进程文件!
> 
> 
> # 日志
> # Specify the server verbosity level.        # 指定服务器的冗长级别。
> # This can be one of:                        # 可能是其中之一:
> # debug (a lot of information, useful for development/testing)        # 大量信息，对开发/测试很有用
> # verbose (many rarely useful info, but not a mess like the debug level)     # 许多很少有用的信息，但不像调试级别那样混乱
> # notice (moderately verbose, what you want in production probably)      # 稍微有点啰嗦，可能是你在生产中想要的(生产环境)
> # warning (only very important / critical messages are logged)     # 只记录非常重要/关键的消息
> loglevel notice      # 日志级别 
> 
> 
> logfile ""       # 日志的文件位置名(如果为空就是标准的输出)
> 
> 
> datebases 16   # 数据库的数量,默认是16个数据库
> 
> always-show-logo no      # 是否总是显示logo(就是通过配置文件启动显示的那个redis logo)
> ```
>
> **快照**
>
> > 持久化,在规定的时间内,执行了多少次操作则会持久化到文件 **.rdb** **.aof** 
> >
> > redis是内存数据库,如果没有持久化,那么数据断电及失!
>
> ```shell
>  save 3600 1     # 如果3600 秒内,如果至少有一个key进行了修改,我们就进行持久化操作
>  save 300 100    # 如果300 秒内,如果至少有100key进行了修改,我们就进行持久化操作 
>  save 60 10000   # 如果60 秒内,如果至少有10000key进行了修改,我们就进行持久化操作
>  # 我们之后学习持久化,会自己定义这个测试!
>  
>  
>  
> stop-writes-on-bgsave-error yes  # 持久化如果出错,是否还需要继续工作!
> 
> rdbcompression yes  # 是否压缩rdb文件,rdb就是持久化的文件,需要消耗一些CPU资源!
> 
> rdbchecksum yes     # 保存rdb文件的时候,进行错误的检查校验!
> 
> dir ./        #  rdb文件保存的目录
> ```
>
> **REPLICATION**     复制,我们后面讲解主从复制的,时候再进行讲解
>
> **SECURITY** 安全
>
> > 可以在这里设置redis的密码,默认是没有密码!
>
> ```shell
> requirepass foobared   # 配置
> 
> # 可以通过客户端查看默认是没有密码的
> 127.0.0.1:6379> config get requirepass       # 获取redis的密码
> 1) "requirepass"
> 2) ""
> 
> # 通过配置文件设置密码
> requirepass 123465     # redis配置文件密码为123456
> 
> # 通过客户端设置密码
> 127.0.0.1:6379> config set requirepass  "123456"        # 设置redis的密码,如果想再次设置为空则需要设置为空字符即可""
> 127.0.0.1:6379> set number 1        # 发现所有的命令都没有权限了
> (error) NOAUTH Authentication required.
> 127.0.0.1:6379> auth 123456       # 使用密码进行登录
> OK
> 127.0.0.1:6379> set number 1
> OK
> ```
>
>  **限制CLIENTS**
>
> ```shell
>  maxclients 10000        # 设置能连接上redis的最大客户端的数量
>  
>  maxmemory <bytes>     # redis 配置对大的内存容量
>  
>  #######################
>  maxmemory-policy noeviction    # 内存到达上线之后的处理策略
>    1. volatile-lru:  只对设置了过期时间的key进行LRU(默认值)
>    2. allkeys-lru:   删除lru算法的key
>    3. volatile-random:  随机删除即将过期key
>    4. allkey-random:    随机删除
>    5. volatile-ttl:     删除即将过期的
>    6. noeviction:       永不过期,返回错误
>  #######################
> ```
>
> **APPEND ONLY MODE 模式 aof配置**
>
> ```shell
> appendonly no    # 默认是不开启aof模式的,默认是使用rdb方式持久化的,在大部分所有的情况下,rdb完全够用
> 
> appendfilename "appendonly.aof"     # 持久化的文件的名字
> 
> # appendfsync always      # 每次修改都会sync 消耗性能
> appendfsync everysec      # 每秒执行一次sync,可能会丢失1s的数据!
> # appendfsync no          # 不执行sync,这个时候操作系统自己同步数据,数度最快!
> ```
>
> 

####   **单位**

```shell
# Redis configuration file example.
#
# Note that in order to read the configuration file, Redis must be
# started with the file path as first argument:
#
# ./redis-server /path/to/redis.conf

# Note on units: when memory size is needed, it is possible to specify
# it in the usual form of 1k 5GB 4M and so forth:
#
# 1k => 1000 bytes
# 1kb => 1024 bytes
# 1m => 1000000 bytes
# 1mb => 1024*1024 bytes
# 1g => 1000000000 bytes
# 1gb => 1024*1024*1024 bytes
#
# units are case insensitive so 1GB 1Gb 1gB are all the same.

################################## INCLUDES ###################################

## 对应中文翻译

 

# Redis配置文件示例。
#
#注意，为了读取配置文件，Redis必须
#以文件路径作为第一个参数开始:
#
# ./redis-server /path/to/redis.conf

#关于单位的注意事项:当需要内存大小时，可以指定
#它通常的形式为1k 5GB 4M等等:
#
# 1k => 1000字节
# 1kb => 1024字节
# 1m => 1000000字节
# 1mb => 1024*1024字节
# 1g => 1000000000字节
# 1gb => 1024*1024*1024字节
#
#单位不区分大小写，所以1GB 1GB 1GB都是一样的。

################################## 包括  ###################################
```

#### 包含

> 就好比我们学习Spring,improt  include

```shell
# Include one or more other config files here.  This is useful if you
# have a standard template that goes to all Redis servers but also need
# to customize a few per-server settings.  Include files can include
# other files, so use this wisely.
#
# Note that option "include" won't be rewritten by command "CONFIG REWRITE"
# from admin or Redis Sentinel. Since Redis always uses the last processed
# line as value of a configuration directive, you'd better put includes
# at the beginning of this file to avoid overwriting config change at runtime.
#
# If instead you are interested in using includes to override configuration
# options, it is better to use include as the last line.
#
# include /path/to/local.conf
# include /path/to/other.conf

######################################################
# 对应中文

#在这里包含一个或多个其他配置文件。这很有用，如果你
#有一个标准模板，适用于所有Redis服务器，但也需要
#自定义一些每个服务器的设置。包含文件可以包含
#其他文件，所以要明智地使用它。
#
#注意选项include不会被命令CONFIG REWRITE重写
#从管理员或Redis哨兵。因为Redis总是使用最后一个处理
# line作为配置指令的值，最好使用includes
#，以避免在运行时覆盖配置更改。
#
#如果您对使用include覆盖配置感兴趣
# options，最好使用include作为最后一行。
#
# include /path/to/local.conf
# include /path/to/other.conf

```

#### 网络

> sda

```shell
################################## NETWORK #####################################

# By default, if no "bind" configuration directive is specified, Redis listens
# for connections from all available network interfaces on the host machine.
# It is possible to listen to just one or multiple selected interfaces using
# the "bind" configuration directive, followed by one or more IP addresses.
# Each address can be prefixed by "-", which means that redis will not fail to
# start if the address is not available. Being not available only refers to
# addresses that does not correspond to any network interfece. Addresses that
# are already in use will always fail, and unsupported protocols will always BE
# silently skipped.
#
# Examples:
#
# bind 192.168.1.100 10.0.0.1     # listens on two specific IPv4 addresses
# bind 127.0.0.1 ::1              # listens on loopback IPv4 and IPv6
# bind * -::*                     # like the default, all available interfaces
#
# ~~~ WARNING ~~~ If the computer running Redis is directly exposed to the
# internet, binding to all the interfaces is dangerous and will expose the
# instance to everybody on the internet. So by default we uncomment the
# following bind directive, that will force Redis to listen only on the
# IPv4 and IPv6 (if available) loopback interface addresses (this means Redis
# will only be able to accept client connections from the same host that it is
# running on).
#
# IF YOU ARE SURE YOU WANT YOUR INSTANCE TO LISTEN TO ALL THE INTERFACES
# JUST COMMENT OUT THE FOLLOWING LINE.
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
bind 127.0.0.1 -::1          # 绑定的地址  如果需要远程访问可以使用通配符*,或者远程公网ip地址

# Protected mode is a layer of security protection, in order to avoid that
# Redis instances left open on the internet are accessed and exploited.
#
# When protected mode is on and if:
#
# 1) The server is not binding explicitly to a set of addresses using the
#    "bind" directive.
# 2) No password is configured.
#
# The server only accepts connections from clients connecting from the
# IPv4 and IPv6 loopback addresses 127.0.0.1 and ::1, and from Unix domain
# sockets.
#
# By default protected mode is enabled. You should disable it only if
# you are sure you want clients from other hosts to connect to Redis
# even if no authentication is configured, nor a specific set of interfaces
# are explicitly listed using the "bind" directive.
protected-mode yes

# Accept connections on the specified port, default is 6379 (IANA #815344).
# If port 0 is specified Redis will not listen on a TCP socket.
port 6379

# TCP listen() backlog.
#
# In high requests-per-second environments you need a high backlog in order
# to avoid slow clients connection issues. Note that the Linux kernel
# will silently truncate it to the value of /proc/sys/net/core/somaxconn so
# make sure to raise both the value of somaxconn and tcp_max_syn_backlog
# in order to get the desired effect.
tcp-backlog 511

# Unix socket.
#
# Specify the path for the Unix socket that will be used to listen for
# incoming connections. There is no default, so Redis will not listen
# on a unix socket when not specified.
#
# unixsocket /run/redis.sock
# unixsocketperm 700

# Close the connection after a client is idle for N seconds (0 to disable)
timeout 0

# TCP keepalive.
#
# If non-zero, use SO_KEEPALIVE to send TCP ACKs to clients in absence
# of communication. This is useful for two reasons:
#
# 1) Detect dead peers.
# 2) Force network equipment in the middle to consider the connection to be
#    alive.
#
# On Linux, the specified value (in seconds) is the period used to send ACKs.
# Note that to close the connection the double of the time is needed.
# On other kernels the period depends on the kernel configuration.
#
# A reasonable value for this option is 300 seconds, which is the new
# Redis default starting with Redis 3.2.1.
tcp-keepalive 300
```



### Redis持久化

> Redis是内存数据库,如果不将内存中的数据库状态保存到磁盘,那么一旦服务器进程推出,服务器中的数据库状态也会消失。所以Redis提供了持久化功能!
>
> 面试和工作,持久化都是重点!

#### RDB(Redis DataBase)

**什么是RDB**

在主从复制中,rdb就是备用的!从机上面!

![image-20230209135159207](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20230209135159207.png)

> 在指定的实践间隔内将内存中的数据集快照写入磁盘,也就是行化讲的Snapshot快照,它恢复时是将快照文件直接读到内存里。
>
> Redis会单独创建(fork)一个子进程来进行持久化,会先将数据写入到一个临时文件中,待持久化过程都结束了,再用这个临时文件替换上次持久化好
>
> 的文件。整个过程中,主进程是不进行任何IO操作的。这就确保了极高的性能。如果需要进行大规模数据的恢复,且对于数据恢复的完整性不是非常
>
> 敏感,那么RDB方式要比AOF方式更加的高效。RDB的缺点是最后一次持久化后的数据可能丢失。
>
> 我们默认的就是RBD,一般情况下不需要修改这个配置!
>
> **有时候在生产环境我们会将这个文件进行备份!**
>
> **rdb**保存的文件是**dump.rdb**文件 都是在我们的配置文件中快照中进行配置的
>
> ```shell
> # /usr/local/redis/redis.conf
> 
> # save 3600 1
> # save 300 100
> # save 60 10000
> save 60  5        # 我们测试一下:只要60s内修改了5次key,就会触发rdb操作
> 
> 
> dbfilename dump.rdb      # 一般会在 /目录下    如果找不到可以用 find / -name dump.rdb 查询
> ```
>
> > **触发机制**
> >
> > 1. save的规则满足的情况下,会自动触发rdb规则
> > 2. 执行flushall命令,也会出发我们的rdb规则!
> > 3. 退出redis,也会产生rdb 文件!
> >
> > 备份就自动生成一个dump.rdb文件
> >
> > **如何恢复rdb文件!**
> >
> > 1. 只需要将rdb文件放在我们redis启动目录就可以,redis启动的时候会自动检查dunm.rdb文件恢复其中的数据!
> > 2. 查看需要存放的位置
> >
> > ```shell
> > 127.0.0.1:6379> CONFIG GET dir
> > 1) "dir"
> > 2) "/"      # 如果在这个目录下存在dump.rdb文件启动的时候就会自动恢复其中的数据
> > ```
> >
> > 几乎就他自己默认的配置就够用了,但是我们还是要去学习!
> >
> > **优点**
> >
> > 1. 适合大规模的数据恢复!   dump.rdb
> > 2. 如果你对数据完整性要求不高!
> >
> > **缺点**
> >
> > 1. 需要一定的时间间隔进程操作! 如果redis意外宕机了,这个最后一次修改数据就没有了!
> > 2. fork 进程的时候,会占用一定的内存空间!
>
> ```shell
> # 测试 rdb
> 127.0.0.1:6379> set k1 v1
> OK
> 127.0.0.1:6379> set k2 v2
> OK
> 127.0.0.1:6379> set k3 v3
> OK
> 127.0.0.1:6379> set k4 v4
> OK
> 127.0.0.1:6379> set k5 v5
> OK
> 127.0.0.1:6379> shutdown # 然后关机
> not connected> exit # 推出redis-cli
> 
> 
> # 重启redis服务
> yaoliuyang@yaoliuyang-PC:~/Documents/study_docs/phpStudyDoc$ systemctl restart redis
> # 再次启动客户端可以用桌面应用程序查看数据依然存在
> yaoliuyang@yaoliuyang-PC:~/Documents/study_docs/phpStudyDoc$ redis-cli
> 127.0.0.1:6379> keys *
> 1) "k4"
> 2) "k5"
> 3) "k2"
> 4) "k1"
> 5) "k3" 
> 
> ```
>
> 

####  AOF(Append Only File)

将我们所有的命令都记录下来,history,恢复的时候就把这个文件全部再执行一遍!

> 是什么

图片

> 以日志的形式来记录每个写操作,将Redis执行过的所有指令记录下来(读操作不记录),只许追加文件但不可以改写文件,redis启动之初会
>
> 自动读取该文件重新构建数据,换言之,redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作

**<font color='red'>Aof保存的是 appendonly.apf文件</font>>**

> append配置    找到自己的redis配置文件 
>
> **appendonly no **默认是 不开启的,我们需要手动进行配置!我们只需要将appendonly改为yes就开启了aof!

```shell
############################## APPEND ONLY MODE ###############################

# By default Redis asynchronously dumps the dataset on disk. This mode is
# good enough in many applications, but an issue with the Redis process or
# a power outage may result into a few minutes of writes lost (depending on
# the configured save points).
#
# The Append Only File is an alternative persistence mode that provides
# much better durability. For instance using the default data fsync policy
# (see later in the config file) Redis can lose just one second of writes in a
# dramatic event like a server power outage, or a single write if something
# wrong with the Redis process itself happens, but the operating system is
# still running correctly.
#
# AOF and RDB persistence can be enabled at the same time without problems.
# If the AOF is enabled on startup Redis will load the AOF, that is the file
# with the better durability guarantees.
#
# Please check https://redis.io/topics/persistence for more information.

appendonly yes      #  默认是不开启的(no)  如果需要开启需要设置成yes

# The name of the append only file (default: "appendonly.aof")

appendfilename "appendonly.aof"    # aof的名字可以修改但不推荐默认的就已经足够了

# appendfsync always        # 每一次都修改 
appendfsync everysec        # 每一秒中同步一次(用这个就可以了,最多只会丢失一秒钟的数据)
# appendfsync no            # 不修改 
```

**测试**

```shell
 # 重启redis让配置文件生效
127.0.0.1:6379> SHUTDOWN      
not connected> exit
yaoliuyang@yaoliuyang-PC:~/Documents/study_docs/phpStudyDoc$ systemctl restart redis
yaoliuyang@yaoliuyang-PC:~/Documents/study_docs/phpStudyDoc$ redis-cli
127.0.0.1:6379> 

# 重启完成之后可以在/目录下看到appendonly.aof文件如果找不到可以使用 find / -name appendonly.aof 命令查询

# 设置几个命令后查看 appendonly.aof 文件
127.0.0.1:6379> set k1 v1 
OK
127.0.0.1:6379> set k2 v2
OK
127.0.0.1:6379> set k3 v3
OK
127.0.0.1:6379> set k4 v4
OK
127.0.0.1:6379> set k5 v5
OK


# 查看 appendonly.aof 文件 可以查看我们的记录 
yaoliuyang@yaoliuyang-PC:/$ cat appendonly.aof      这个文件中记录着我们的所有写操作
*2
$6
SELECT
$1
0
*3
$3
set
$2
k1
$2
v1
*3
$3
set
$2
k2
$2
v2
*3
$3
set
$2
k3
$2
v3
*3
$3
set
$2
k4
$2
v4
*3
$3
set
$2
k5
$2
v5

# 我们尝试破坏appendonly.aof 文件内容
yaoliuyang@yaoliuyang-PC:/$ vim appendonly.aof     # 在文件的最后随便添加点字符改动文件
*2
$6
SELECT
$1
0
*3
$3
set
$2
k1
$2
v1
*3
$3
set
$2
k2
$2
v2
*3
$3
set
$2
k3
$2
v3
*3
$3
set
$2
k4
$2
v4
*3
$3
set
$2
k5
$2
dsaaaaaaaaaaaaaaaaaav5      # 修改破坏了文件

# 重新启动reids
127.0.0.1:6379> SHUTDOWN
not connected> exit # 推出cli 
yaoliuyang@yaoliuyang-PC:~/Documents/study_docs/phpStudyDoc$ systemctl restart redis
yaoliuyang@yaoliuyang-PC:~/Documents/study_docs/phpStudyDoc$ redis-cli
Could not connect to Redis at 127.0.0.1:6379: Connection refused       #连接拒绝 aof文件有问题
not connected> 


# 如果 这个aof文件有错误,这时候redis是启动不起来的,我们需要修复这个aof配置文件
## redis给我们提供了一个工具  redis-check-aof --fix +aof文件路径

yaoliuyang@yaoliuyang-PC:/usr/local/redis/src$ sudo redis-check-aof --fix /appendonly.aof 
0x              a4: Expected \r\n, got: 6161
AOF analyzed: size=188, ok_up_to=139, ok_up_to_line=40, diff=49
This will shrink the AOF from 188 bytes, with 49 bytes, to 139 bytes
Continue? [y/N]: y
Successfully truncated AOF

# 再次查看配置文件
yaoliuyang@yaoliuyang-PC:/$ cat appendonly.aof 
*2
$6
SELECT
$1
0
*3
$3
set
$2
k1
$2
v1
*3
$3
set
$2
k2
$2
v2
*3
$3
set
$2
k3
$2
v3
*3
$3
set
$2
k4
$2
v4

# 再次启动redis
## 如果systemctl restart redis 或者 service redis restart 无法启动请使用 sudo ./redis-server /etc/redis/6379.conf启动


```

**重写规则**

> 重写规则
>
> aof默认就是文件的无限追加,文件会越来越大!

```sh
no-appendfsync-on-rewrite no


auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

# 如果aof文件大于64M,太大了!fork一个新的进程来讲我们的文件进行重写!
```



**aof优点&缺点**

**优点**

1. 每一次修改都同步,文件的完整性会更加好!
2. 每秒同步一次,可能会修饰一秒的数据
3. 从不同步,效率最高的!

**缺点**

1. 相对于数据文件来说,aof远远大于rdb,修复的速度也比rdb慢!
2. Aof运行效率也要比rdb慢,所以我们redis默认的配置就是rdb持久化!

**扩展:**

1. RDB持久化方式能够在指定的时间间隔内对你的数据进行快照存储
2. AOF持久化方式记录每次对服务器写的操作,当服务器重启的时候会重新执行这些命令来恢复原始的数据,AOF命令以Redis协议追加保存每次写的操作到文件末尾,Redis还能对AOF文件进行后台重写,使AOF文件的体积不至于过大
3. 只做缓存,如果你希望你的数据在服务器运行的时候存在,你也可以不使用任何持久化
4. 同时开启两种持久化方式

- 在这种情况下,当redis重启的时候会优先载入AOF文件来恢复原始的数据,因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整
- RDB的数据不实时,同时使用两者时服务器重启也只会找AOF文件,那要不要只使用AOF呢?作者建议不要,因为RDB更合适用于备份数据库(AOF在不断变化不好备份),快速重启,而且不会有AOF可能潜在的Bug,留着作为一个万一的手段

5 . 性能建议

- 因为RDB文件值用作后备用途,建议只在Slave上持久化RDB文件,而且只要15分钟备份一次就够了,只保留save 900 1(15分钟内只要超过1条数据修改那么他就会RDB规则) 这条规则
- 如果Enable AOF,好处是在最恶劣情况下也只会丢失不超过两秒数据,启动脚本较简单只load自己的AOF文件就可以了,代价一是带来了持续的IO,

二是AOF rewrite的频率的将rewrite过程中产生的新数据写到新文件造成 的阻塞几乎是不可避免的。只要因盘许可,应该尽量减少AOF rewrite的频率,

AOF重写的基础大小默认值64M太小了,可以设置到5G以上,默认超过原大小100%大小重写可以改到适当的数值。

- 如果不Enable AOF,仅靠Master-Slave Repllcation 实现高可用也可以,能省掉一大笔IO,也减少了rewrite时带来的系统波动。代价是

如果Master/Slave(主从)同时宕掉(断电),会丢失十几分钟的数据,启动脚本也要比较两个Master/Slave中的RDB文件,载入较新的那个,微博就是这种架构。







### [Redis发布订阅](https://www.runoob.com/redis/redis-pub-sub.html)

> Redis发布订阅(pub/sub)是一种**消息通信模式**:发送者(pub)发送消息,订阅者(sub)接收消息。  微信,微博客,关注系统!
>
> Redis客户端可以订阅任意数量的频道。
>
> 订阅/发布消息图:

第一个: 消息发送者，第二个:频道    第三个:消息订阅者

![img](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/2404975-20220726225800125-2023426694.png)



下图展示了频道channel1,以及订阅这个频道的三个客户端--client2,client5和client1之间的关系:

![img](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/pubsub1.png)



当有新消息通过PUBLISH命令发送给频道channel1时,这个消息就会被发送给订阅它的三个客户端:

![img](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/pubsub2.png)

#### Redis 发布订阅命令

下表列出了 redis 发布订阅常用命令：

> 这些命令被广泛用于构建即使通信应用,比如网络聊天室(chatroom)和实时广播,实时提醒等.

| 序号 | 命令及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | [PSUBSCRIBE pattern [pattern ...\]](https://www.runoob.com/redis/pub-sub-psubscribe.html) 订阅一个或多个符合给定模式的频道。 |
| 2    | [PUBSUB subcommand [argument [argument ...\]]](https://www.runoob.com/redis/pub-sub-pubsub.html) 查看订阅与发布系统状态。 |
| 3    | [PUBLISH channel message](https://www.runoob.com/redis/pub-sub-publish.html) 将信息发送到指定的频道(消息发送者)。 |
| 4    | [PUNSUBSCRIBE [pattern [pattern ...\]]](https://www.runoob.com/redis/pub-sub-punsubscribe.html) 退订所有给定模式的频道。 |
| 5    | [SUBSCRIBE channel [channel ...\]](https://www.runoob.com/redis/pub-sub-subscribe.html) 订阅给定的一个或多个频道的信息。 |
| 6    | [UNSUBSCRIBE [channel [channel ...\]]](https://www.runoob.com/redis/pub-sub-unsubscribe.html) 指退订给定的频道。 |

#### 测试

**订阅端:**

```shell
yaoliuyang@yaoliuyang-PC:/usr/local/redis/src$ redis-cli
127.0.0.1:6379> SUBSCRIBE yaoliuyang         # 订阅一个频道 yaoliuyang
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "yaoliuyang"
3) (integer) 1        # 等待读取推送的信息
1) "message"        # 消息
2) "yaoliuyang"       # 那个频道的消息
3) "\xe4\xbd\xa0\xe5\xa5\xbd"     # 消息的具体内容
```

**发送端:**

```shell
yaoliuyang@yaoliuyang-PC:~/Documents/study_docs/phpStudyDoc$ redis-cli    
127.0.0.1:6379> PUBLISH yaoliuyang "你好"       #  发布者发布消息到频道
(integer) 1 
```

**原理**

> Redis是使用C实现的,通过分析Redis源码里的pubsub.c文件,了解发布和订阅机制的底层实现,籍此加深对Redis的理解.
>
> Redis通过PUBLISH(**推送**)，SUBSCRIBE(**接收**)和PSUBSCRIBE(**接收多个**)等命令实现发布和订阅功能
>
> 通过哦SUBSCRIBE命令订阅某频道后,redis-server里维护了一个字典,字典的键就是一个个channel(**频道**)，而字典的值则是一个链表,
>
> 链表中保存了所有订阅这个channel的客户端.SUBSCRIBE命令的关键,就是将客户端加到给定channel的订阅链表中.
>
> 通过PUBLISH命令向订阅者发送消息,redis-server会使用给定的频道作为键,在它所维护的channel字典中查找记录了订阅这个频道的
>
> 所有客户端的链表,遍历这个链表,将消息发布给所有订阅者.
>
> Pub/Sub从字面上理解就是发布(Publish)与订阅(Subscribe),在Redis中,你可以设定对某一个key值进行消息发布及消息订阅,当一个key值上面进行了消息发布后,所有订阅它的客户端都会收到相应的消息.这一功能最明显的用法就是用作实时消息系统,比如普通的及时聊天,群聊等功能.
>
> **使用场景:**
>
> 1. 实时消息系统!
> 2. 实时聊天(频道当作聊天室,将信息回显给所有人即可!)!
> 3. 订阅,关注系统都是可以的!
>
> **稍微复杂的场景我们就会使用消息中间件MQ**

### Redis主从复制

#### 概念

主从复制,是指将一台Redis服务器的数据,复制到其他的Redis服务器.前者称为主节点(master/leader),后者称为从节点(slave/follower);

**<font color='red'>数据的复制是单向的,只能由主节点到从节点</font>**.Master以写为主,Slave以读为主.

<font color='red'>默认情况下,每台Redis服务器都是主节点;</font>且一个主节点可以有多个从节点(或没有从节点),但一个从节点只能有一个主节点.

**主从复制的作用包括:**

1. 数据冗余: 主从复制实现了数据的热备份,是持久化之外的一种数据冗余方式
2. 故障恢复: 当主节点出现问题时,可以由节点提供服务,实现快速的故障恢复;实际上是一种服务的冗余
3. 负载均衡: 在主从复制的基础上,配合读写分离,可以由主节点提供写服务,由从节点提供读服务(即写Redis数据时应用连接主节点,读Redis数据时应用连接从节点),分担服务器负载;尤其是在写少读多的场景下,通过多个从节点分担读负载,可以大大提高Redis服务器的并发量
4. 高可用基石: 除了上述作用以外,主从复制还是哨兵和集群能够实施的基础,因此说主从复制是Redis高可用的基础.

一般来说,要将Redis运用于工程项目中,只使用一台Redis是万万不能的(宕机,一主二从),原因如下:

1. 从结构上,单个Redis服务器会发生单点故障,并且一台服务器需要处理所有的请求负载,压力较大;
2. 从容量上,单个Redis服务器内存容量有限,就算一台Redis服务器内存容量为256G,也不难将所有的内存用作Redis存储内存,一般来说,<font color='red'>单台Redis最大使用内存不因该超过20G</font>

电商商战上的商品,一般都是一次上传,无数次阅览的,说专业点也就是"多读少写"

对于这种场景,我们可以使用如下这种架构

![img](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/u=2063238625,1214794687&fm=253&fmt=auto&app=138&f=PNG)

主从复制,读写分离!80%的情况下都是在进行读操作!减缓服务器的压力!架构中经常使用! 一主二从!

只要在公司中,主从复制就是必须要使用的,因为在真实的项目中不可能单机使用Redis!





#### 环境配置

> 只配置从库,不配值主库

```shell
127.0.0.1:6379> info Replication       # 查看当前库的信息
# Replication
role:master           # 角色
connected_slaves:0      # 没有从机
master_failover_state:no-failover
master_replid:014d76269b049337bbcb434e6d0007bbfe2a1c84
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0


# 添加多几个配置文件修改下面几个参数(本地模拟可以复制几个配置文件 例如 cp 6379.conf 6371.conf,cp 6379.conf 6372.conf)
## 复制三个文件,然后修改对应的信息
port 6371         # 端口(进程占用的端口号)
daemonize yes                 
pidfile /var/run/redis_6371.pid       # pid名字(port id:记录了进程的id,文件带有锁.可以防止程序的多次启动)
logfile "6371.log"               # log文件名字(logfile:明确日志文件的位置)
dbfilename dump6371.rdb          # dump.rdb文件名字(持久化文件位置)

# 启动自定义的配置 (如果无法启动请先关闭配置文件中的daemonize然后再启动就可以看见错误信息)
/usr/local/redis/src/redis-server /etc/redis/6371.conf 

# 启动三个redis(可以使用进程命令查看)
yaoliuyang@yaoliuyang-PC:/etc/redis$ ps -ef | grep redis
.........
root       77219       1  0 15:58 ?        00:00:01 /usr/local/redis/src/redis-server 127.0.0.1:6379
yaoliuy+   79385       1  0 17:14 ?        00:00:00 redis-server 127.0.0.1:6371
yaoliuy+   79567       1  0 17:17 ?        00:00:00 redis-server 127.0.0.1:6372

```

#### 一主二从

#### <font color='red'>工作中不会使用</font>

> <font color='red'>默认情况下,每台Redis服务器都是主节点;</font> 我们一般情况下只用配置从机就好了!
>
> 认老大! 一主(可以是三台中的任何一台建议6379) 二从(6371,6372)

```shell
# 查看配置的redis是不是主机(这个没啥用只是看一下)
yaoliuyang@yaoliuyang-PC:~/Documents/study_docs/phpStudyDoc$ redis-cli -p 6371
127.0.0.1:6371> INFO replication
# Replication
role:master
connected_slaves:0
master_failover_state:no-failover
master_replid:00f346e93584759caf330795cd46dd61860d27d5
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0

# 配置从机 6371(仆)

127.0.0.1:6371> SLAVEOF 127.0.0.1 6379     # SLAVEOF host   6371 认 6379当老大
OK
127.0.0.1:6371> info replication
# Replication
role:slave           # 再次查看可以查看角色是从机
master_host:127.0.0.1 # 可以看到主机的信息
master_port:6379        #
master_link_status:down
master_last_io_seconds_ago:-1
master_sync_in_progress:0
slave_read_repl_offset:1
slave_repl_offset:1
master_link_down_since_seconds:-1
slave_priority:100
slave_read_only:1
replica_announced:1
connected_slaves:0
master_failover_state:no-failover
master_replid:00f346e93584759caf330795cd46dd61860d27d5
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
(3.74s)

# 在主机中查看
127.0.0.1:6379> info replication
# Replication
role:master
connected_slaves:1     # 多了从机的配置
slave0:ip=127.0.0.1,port=6371,state=online,offset=0,lag=1
master_failover_state:no-failover
master_replid:ddf87230d913f778d862c507a7ab47d6820ccde7
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:279
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:279

# 配置第二台从机
yaoliuyang@yaoliuyang-PC:/$ redis-cli -p 6372
127.0.0.1:6372> SLAVEOF 127.0.0.1 6379
OK
127.0.0.1:6372> info replication
# Replication
role:slave
master_host:127.0.0.1
master_port:6379
master_link_status:down
master_last_io_seconds_ago:-1
master_sync_in_progress:0
slave_read_repl_offset:1
slave_repl_offset:1
master_link_down_since_seconds:-1
slave_priority:100
slave_read_only:1
replica_announced:1
connected_slaves:0
master_failover_state:no-failover
master_replid:4053b2013720a406983a724face257230cb1dd25
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
(0.94s)

# 再次查看主机    如果两个都配置完了,就是有两个从机的
## 补充 如果6379(主)的配置文件中redis设置了密码,就再去71&72文件中添加一个配置 masterauth 密码
127.0.0.1:6379> info replication
# Replication
role:master
connected_slaves:2
slave0:ip=127.0.0.1,port=6372,state=online,offset=0,lag=3
slave1:ip=127.0.0.1,port=6371,state=online,offset=0,lag=2
master_failover_state:no-failover
master_replid:ddf87230d913f778d862c507a7ab47d6820ccde7
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:545
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:545
```

**<font color='red'>真实的主从配置应该在配置文件中配置,这样的话是永久的</font>** 我们这里使用的是命令(暂时的)

```shell
# sudo vim 6371.conf

# replicaof <masterip> <masterport>  # 取消注释
replicaof 127.0.0.1 6379	# 编辑成这种

# masterauth <master-password> # 如果主机有密码记得配置主机的密码
masterauth 123456
```

#### 细节了解

> 主机可以写,从机不能写只能读!主机中的所有信息和数据,都会自动被从机保存!
>
> 主机写:
>
> ```shell
> 127.0.0.1:6379> keys *
> (empty array)
> 127.0.0.1:6379> set name 123
> OK
> 
> ```
>
> 从机只能读取内容!
>
> ```shell
> 127.0.0.1:6372> keys *
> 1) "name"
> 127.0.0.1:6372> set age 2
> (error) READONLY You can't write against a read only replica.
> 127.0.0.1:6372> 
> ```



测试:主机断开连接,从机一就是连接到主机的,但是没有写操作,这个时候,主机如果回来了,从机依旧可以直接获取到到主机写的信息!



如果是使用命令行来配置的主从,这个时候如果重启了,就会编程主机(即shutdown关闭后再次启动连接info Replication可以查看),只要

变为了从机,立马就会从主机中获取值!

**复制原理**

> Slave 启动成功连接到master后会发送一个sync同步命令
>
> Master接到命令,启动后台的存盘进程,同时收集所有接收到的用于修改数据集命令,在后台进程执行完毕之后,<font color='red'>master将传送整个数据文件到slave,并完成一次数据同步.</font>
>
> <font color="red">全量复制</font>: 而slave服务在接收到数据库文件数据后,将其存盘并加载到内存中
>
> <font color='red'>增量复制</font>: Master继续将新的所有收集到的修改命令一次传给slave,完成同步  （增量复制意思就是断开重写连接进行一次全量复制之后再次在主机中写入一个值就会增量复制到断开连接后全量复制一遍后的从机上）   
>
> <font color='red'>但是只要是重新连接master,一次完全同步(全量复制)将被自动执行</font>   我们的数据一定可以在从机中看到!

#### 层层链路(毛毛虫玩法:上一个M链接下一个S)

#### <font color='red'>工作中不会使用</font>

>  上面讲的一主二从是将6371&6372分别都连接到主节点6379 这样有一个弊端如果主节点断了,那么两个子节点(6371&6372相当于废了)
>
> 现在我们重新配置一下    6379依然是主节点   6371 绑定主机 6379   6372绑定从机6371
>
>   

```shell
#################################################

# 6379
127.0.0.1:6379> info Replication
# Replication
role:master
connected_slaves:1
slave0:ip=127.0.0.1,port=6371,state=online,offset=2027,lag=0
master_failover_state:no-failover
master_replid:bf5a2aca09516e1db76f0ac8485b3b4786874355
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:2027
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:2027


# 6371 

127.0.0.1:6371> info Replication
# Replication
role:slave            	
master_host:127.0.0.1
master_port:6379
master_link_status:up
master_last_io_seconds_ago:4
master_sync_in_progress:0
slave_read_repl_offset:2125
slave_repl_offset:2125
slave_priority:100
slave_read_only:1
replica_announced:1
connected_slaves:1
slave0:ip=127.0.0.1,port=6372,state=online,offset=2125,lag=0
master_failover_state:no-failover
master_replid:bf5a2aca09516e1db76f0ac8485b3b4786874355
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:2125
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:2125

# 6372

127.0.0.1:6372> info 127.0.0.1:6371> info Replication
ERR syntax error
127.0.0.1:6372> info Replication
# Replication
role:slave
master_host:127.0.01
master_port:6371
master_link_status:up
master_last_io_seconds_ago:9
master_sync_in_progress:0
slave_read_repl_offset:2167
slave_repl_offset:2167
slave_priority:100
slave_read_only:1
replica_announced:1
connected_slaves:0
master_failover_state:no-failover
master_replid:bf5a2aca09516e1db76f0ac8485b3b4786874355
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:2167
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:642
repl_backlog_histlen:1526
```

**如果没有老大了,这个时候能不能选者一个老大出来呢?手动!**

> 谋朝篡位
>
> 如果主机断开了连接,我们可以使用`SLAVEOF no one` 让自己变成主机!其他的节点就可以手动连接到最新的这个主节点(手动)
>
> 如果这个时候老大修复了,那就重新连接!

### [哨兵模式](https://www.jianshu.com/p/f439da2b2d2d)

http://c.biancheng.net/redis/sentinel-model.html

(自动选举老大的模式)

**概述**

主从切换技术的方法是:当主服务器宕机后,需要手动把一台服务器切换为主服务器,这就需要人工干预,费时费力,还会造成一段时间内服务不可用

这不是一种推荐的方式,更多时候,我们有限考虑哨兵模式.Redis从2.8开始正式提供了Sentinel(哨兵)架构来解决这个问题

谋朝篡位的自动版,能够后台监控主机是否故障,如果故障了根据投票数<font color='red'>自动将从库转换为主库。</font>

哨兵模式是一种特殊的模式,首先Redis提供了哨兵命令,哨兵是一个独立的进程,作为进程,他会独立运行.其原理是**哨兵通过发送命令,等待Redis服务器响应,从而监控运行多个Redis实例**



![img](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/a9a46e8f72464c75947658754d17da33.png)

这里的哨兵有两个作用

- 通过发送命令,让Redis服务器返回监控其运行状态,包括主服务器和从服务器。
- 当哨兵检测到master宕机,会自动将slave切换成master,然后通过发布订阅模式通知其他的从该服务器,修改配置文件,让它们切换主机.

然而一个哨兵进程对Redis服务器进行监控,可能会出现问题,为此,我们可以使用多个哨兵进行监控。各个哨兵之间还会进行监控,这样就形成了多个哨兵模式。

![img](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/9571610-ab6167bd8963734d.png)

假设服务器宕机,哨兵1先检测到这个结果,系统并不会马上进行failover过程,仅仅是哨兵1主管认为主服务器不可用,这个现象成为**主观下线**.

当后面的哨兵也检测到主服务器不可用,并且数量到达一定值时,那么哨兵之间julius进行一次投票,投票的结果由一个哨兵发起,进行failover[故障转移]

操作。切换成功后,就会通过发布订阅模式,让各个哨兵把自己 监控的从服务器实现切换主机,这个过程称为**客观下线**

#### 测试

> 我们目前的状态是一主二从!

1. 配置哨兵配置文件

> sudo vim sentinel.conf

    ```shell
    # sudo vim sentinel.conf host port  1
    # sentinel monitor 被监控的服务器的名称（可以随意起） host port 1
    #后面的这个数字1代表主机挂了，slave从机投票看让谁接替为主机，票数最多的，就会成为主机！
    sentinel  monitor myredis 127.0.0.1 6379 1 
    
    #如果被监控的主机redis服务器有密码的话，还有在sentinel.conf文件中加上认证密码
    # sentinel auth-pass 被监控的服务器的名称（可以随意起） password
    sentinel auth-pass myredis 123456
    ```

2. 启动哨兵

```shell
yaoliuyang@yaoliuyang-PC:/etc/redis$ redis-sentinel /etc/redis/sentinel.conf 
12900:X 12 Feb 2023 20:52:37.985 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
12900:X 12 Feb 2023 20:52:37.985 # Redis version=6.2.6, bits=64, commit=00000000, modified=0, pid=12900, just started
12900:X 12 Feb 2023 20:52:37.985 # Configuration loaded
12900:X 12 Feb 2023 20:52:37.985 * Increased maximum number of open files to 10032 (it was originally set to 1024).
12900:X 12 Feb 2023 20:52:37.985 * monotonic clock: POSIX clock_gettime
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 6.2.6 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                  
 (    '      ,       .-`  | `,    )     Running in sentinel mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 26379
 |    `-._   `._    /     _.-'    |     PID: 12900
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           https://redis.io       
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

12900:X 12 Feb 2023 20:52:37.986 # Could not create tmp config file (Permission denied)
12900:X 12 Feb 2023 20:52:37.986 # WARNING: Sentinel was not able to save the new configuration on disk!!!: Permission denied
12900:X 12 Feb 2023 20:52:37.986 # Sentinel ID is 61cc641b1e3485f37b2f296de083df3d67655c8c
12900:X 12 Feb 2023 20:52:37.986 # +monitor master myredis 127.0.0.1 6379 quorum 1
12900:X 12 Feb 2023 20:52:37.988 * +slave slave 127.0.0.1:6371 127.0.0.1 6371 @ myredis 127.0.0.1 6379
12900:X 12 Feb 2023 20:52:37.988 # Could not create tmp config file (Permission denied)
12900:X 12 Feb 2023 20:52:37.988 # WARNING: Sentinel was not able to save the new configuration on disk!!!: Permission denied

```

如果Master节点断开了,这个时候就会从从机中随机选择一个服务器!(这里面有一个投票算法!)

```shell
# 哨兵日志
12900:X 12 Feb 2023 20:52:37.986 # Could not create tmp config file (Permission denied)
12900:X 12 Feb 2023 20:52:37.986 # WARNING: Sentinel was not able to save the new configuration on disk!!!: Permission denied
12900:X 12 Feb 2023 20:52:37.986 # Sentinel ID is 61cc641b1e3485f37b2f296de083df3d67655c8c
12900:X 12 Feb 2023 20:52:37.986 # +monitor master myredis 127.0.0.1 6379 quorum 1
12900:X 12 Feb 2023 20:52:37.988 * +slave slave 127.0.0.1:6371 127.0.0.1 6371 @ myredis 127.0.0.1 6379
12900:X 12 Feb 2023 20:52:37.988 # Could not create tmp config file (Permission denied)
12900:X 12 Feb 2023 20:52:37.988 # WARNING: Sentinel was not able to save the new configuration on disk!!!: Permission denied
12900:X 12 Feb 2023 20:55:08.551 * +slave slave 127.0.0.1:6372 127.0.0.1 6372 @ myredis 127.0.0.1 6379
12900:X 12 Feb 2023 20:55:08.551 # Could not create tmp config file (Permission denied)
12900:X 12 Feb 2023 20:55:08.551 # WARNING: Sentinel was not able to save the new configuration on disk!!!: Permission denied
12900:X 12 Feb 2023 20:56:14.520 # +sdown master myredis 127.0.0.1 6379
12900:X 12 Feb 2023 20:56:14.520 # +odown master myredis 127.0.0.1 6379 #quorum 1/1
12900:X 12 Feb 2023 20:56:14.520 # +new-epoch 1
12900:X 12 Feb 2023 20:56:14.520 # +try-failover master myredis 127.0.0.1 6379
12900:X 12 Feb 2023 20:56:14.520 # Could not create tmp config file (Permission denied)
12900:X 12 Feb 2023 20:56:14.521 # WARNING: Sentinel was not able to save the new configuration on disk!!!: Permission denied
12900:X 12 Feb 2023 20:56:14.521 # +vote-for-leader 61cc641b1e3485f37b2f296de083df3d67655c8c 1
12900:X 12 Feb 2023 20:56:14.521 # +elected-leader master myredis 127.0.0.1 6379
12900:X 12 Feb 2023 20:56:14.521 # +failover-state-select-slave master myredis 127.0.0.1 6379
12900:X 12 Feb 2023 20:56:14.583 # +selected-slave slave 127.0.0.1:6371 127.0.0.1 6371 @ myredis 127.0.0.1 6379
12900:X 12 Feb 2023 20:56:14.583 * +failover-state-send-slaveof-noone slave 127.0.0.1:6371 127.0.0.1 6371 @ myredis 127.0.0.1 6379
# failover 故障转移
12900:X 12 Feb 2023 20:56:14.635 * +failover-state-wait-promotion slave 127.0.0.1:6371 127.0.0.1 6371 @ myredis 127.0.0.1 6379
12900:X 12 Feb 2023 20:56:14.870 # Could not create tmp config file (Permission denied)
12900:X 12 Feb 2023 20:56:14.870 # WARNING: Sentinel was not able to save the new configuration on disk!!!: Permission denied
12900:X 12 Feb 2023 20:56:14.870 # +promoted-slave slave 127.0.0.1:6371 127.0.0.1 6371 @ myredis 127.0.0.1 6379
12900:X 12 Feb 2023 20:56:14.870 # +failover-state-reconf-slaves master myredis 127.0.0.1 6379
12900:X 12 Feb 2023 20:56:14.933 * +slave-reconf-sent slave 127.0.0.1:6372 127.0.0.1 6372 @ myredis 127.0.0.1 6379
12900:X 12 Feb 2023 20:56:15.913 * +slave-reconf-inprog slave 127.0.0.1:6372 127.0.0.1 6372 @ myredis 127.0.0.1 6379
12900:X 12 Feb 2023 20:56:15.913 * +slave-reconf-done slave 127.0.0.1:6372 127.0.0.1 6372 @ myredis 127.0.0.1 6379
12900:X 12 Feb 2023 20:56:15.968 # +failover-end master myredis 127.0.0.1 6379
12900:X 12 Feb 2023 20:56:15.968 # +switch-master myredis 127.0.0.1 6379 127.0.0.1 6371
12900:X 12 Feb 2023 20:56:15.968 * +slave slave 127.0.0.1:6372 127.0.0.1 6372 @ myredis 127.0.0.1 6371
12900:X 12 Feb 2023 20:56:15.968 * +slave slave 127.0.0.1:6379 127.0.0.1 6379 @ myredis 127.0.0.1 6371
12900:X 12 Feb 2023 20:56:15.969 # Could not create tmp config file (Permission denied)
12900:X 12 Feb 2023 20:56:15.969 # WARNING: Sentinel was not able to save the new configuration on disk!!!: Permission denied
12900:X 12 Feb 2023 20:56:46.011 # +sdown slave 127.0.0.1:6379 127.0.0.1 6379 @ myredis 127.0.0.1 6371
```

**哨兵模式**

> 如果此时主机回来了,只能归并到新的主机下,当作从机,这就是哨兵模式的规则!

**优点:**

1. 哨兵集群,基于主从复制模式,所有的主从配置优点,它全有
2. 主从可以切换,故障可以转移,系统的可用性就会更好
3. 哨兵模式就是主从模式的升级,手动到自动,更加见健壮

**缺点:**

1. Redis 不好在线扩容的,集群容量一旦到达上线,在线扩容就十分麻烦!
2. 实现哨兵模式的配置其实是很麻烦的,里面有很多选择!

**哨兵模式的全部配置**



| 配置项                                      | 参数类型                   | 说明                                                         |
| ------------------------------------------- | -------------------------- | ------------------------------------------------------------ |
| dir                                         | 文件目录                   | 哨兵进程服务的文件存放目录，默认为 /tmp。                    |
| port                                        | 端口号                     | 启动哨兵的进程端口号，默认为 26379。                         |
| sentinel down-after-milliseconds            | <服务名称><毫秒数(整数)>   | 在指定的毫秒数内，若主节点没有应答哨兵的 PING 命令，此时哨兵认为服务器主观下线，默认时间为 30 秒。 |
| sentinel parallel-syncs                     | <服务名称><服务器数(整数)> | 指定可以有多少个 Redis 服务同步新的主机，一般而言，这个数字越小同步时间越长，而越大，则对网络资源要求就越高。 |
| sentinel failover-timeout                   | <服务名称><毫秒数(整数)>   | 指定故障转移允许的毫秒数，若超过这个时间，就认为故障转移执行失败，默认为 3 分钟。 |
| sentinel notification-script                | <服务名称><脚本路径>       | 脚本通知，配置当某一事件发生时所需要执行的脚本，可以通过脚本来通知管理员，例如当系统运行不正常时发邮件通知相关人员。 |
| sentinel auth-pass <master-name> <password> | <服务器名称><密码>         | 若主服务器设置了密码，则哨兵必须也配置密码，否则哨兵无法对主从服务器进行监控。该密码与主服务器密码相同。 |



```shell
 
# Example sentinel.conf
 
# 哨兵sentinel实例运行的端口 默认26379
# 如果有哨兵集群 需要配置多个端口
port 26379
 
# 哨兵sentinel的工作目录
dir /tmp
 
# 哨兵sentinel监控的redis主节点的 ip port
# master-name 可以自己命名的主节点名字 只能由字母A-z、数字0-9 、这三个字符".-_"组成。
# quorum 当这些quorum个数sentinel哨兵认为master主节点失联 那么这时 客观上认为主节点失联了
# sentinel monitor <master-name> <ip> <redis-port> <quorum>
sentinel monitor mymaster 127.0.0.1 6379 1
 
# 当在Redis实例中开启了requirepass foobared 授权密码 这样所有连接Redis实例的客户端都要提供密码
# 设置哨兵sentinel 连接主从的密码 注意必须为主从设置一样的验证密码
# sentinel auth-pass <master-name> <password>
sentinel auth-pass mymaster MySUPER--secret-0123passw0rd
 
 
# 指定多少毫秒之后 主节点没有应答哨兵sentinel 此时 哨兵主观上认为主节点下线 默认30秒
# sentinel down-after-milliseconds <master-name> <milliseconds>
sentinel down-after-milliseconds mymaster 30000
 
# 这个配置项指定了在发生failover主备切换时最多可以有多少个slave同时对新的master进行 同步，
# 这个数字越小，完成failover所需的时间就越长，
# 但是如果这个数字越大，就意味着越 多的slave因为replication而不可用。
# 可以通过将这个值设为 1 来保证每次只有一个slave 处于不能处理命令请求的状态。
# sentinel parallel-syncs <master-name> <numslaves>
sentinel parallel-syncs mymaster 1
 
 
 
# 故障转移的超时时间 failover-timeout 可以用在以下这些方面：
#1. 同一个sentinel对同一个master两次failover之间的间隔时间。
#2. 当一个slave从一个错误的master那里同步数据开始计算时间。直到slave被纠正为向正确的master那里同步数据时。
#3.当想要取消一个正在进行的failover所需要的时间。
#4.当进行failover时，配置所有slaves指向新的master所需的最大时间。不过，即使过了这个超时，slaves依然会被正确配置为指向master，但是就不按parallel-syncs所配置的规则来了
# 默认三分钟
# sentinel failover-timeout <master-name> <milliseconds>
sentinel failover-timeout mymaster 180000
 
# SCRIPTS EXECUTION
 
#配置当某一事件发生时所需要执行的脚本，可以通过脚本来通知管理员，例如当系统运行不正常时发邮件通知相关人员。
#对于脚本的运行结果有以下规则：
#若脚本执行后返回1，那么该脚本稍后将会被再次执行，重复次数目前默认为10
#若脚本执行后返回2，或者比2更高的一个返回值，脚本将不会重复执行。
#如果脚本在执行过程中由于收到系统中断信号被终止了，则同返回值为1时的行为相同。
#一个脚本的最大执行时间为60s，如果超过这个时间，脚本将会被一个SIGKILL信号终止，之后重新执行。
 
#通知型脚本:当sentinel有任何警告级别的事件发生时（比如说redis实例的主观失效和客观失效等等），将会去调用这个脚本，
#这时这个脚本应该通过邮件，SMS等方式去通知系统管理员关于系统不正常运行的信息。调用该脚本时，将传给脚本两个参数，
#一个是事件的类型，
#一个是事件的描述。
#如果sentinel.conf配置文件中配置了这个脚本路径，那么必须保证这个脚本存在于这个路径，并且是可执行的，否则sentinel无法正常启动成功。
#通知脚本
# sentinel notification-script <master-name> <script-path>
sentinel notification-script mymaster /var/redis/notify.sh
 
# 客户端重新配置主节点参数脚本
# 当一个master由于failover而发生改变时，这个脚本将会被调用，通知相关的客户端关于master地址已经发生改变的信息。
# 以下参数将会在调用脚本时传给脚本:
# <master-name> <role> <state> <from-ip> <from-port> <to-ip> <to-port>
# 目前<state>总是“failover”,
# <role>是“leader”或者“observer”中的一个。
# 参数 from-ip, from-port, to-ip, to-port是用来和旧的master和新的master(即旧的slave)通信的
# 这个脚本应该是通用的，能被多次调用，不是针对性的。
# sentinel client-reconfig-script <master-name> <script-path>
sentinel client-reconfig-script mymaster /var/redis/reconfig.sh # 运维配置
```



#### 扩展补充

**laravel哨兵配置连接**

| 名称     | 地址                                                 |
| -------- | ---------------------------------------------------- |
| 网络博客 | [link](https://www.freesion.com/article/8524774342/) |



### Redis缓存穿透和雪崩(面试高频,工作常用!)

>  这里仅仅是了解，不涉及解决方案底层

Redis缓存的使用，极大的提升了应用程序的性能和效率，特别是数据查询方面。但同时，它带来了一些问题。其中最要害的问题，就是数据一致性问题，从严格意义上讲，这个问题无解。如果对数据的一致性要求很高，那么就不能使用缓存。另外的一些典型问题就是，缓存穿透、缓存雪崩和缓存击穿。目前，业界也都有比较流行的解决方案。

#### **缓存穿透(查不到)**

> 概念

缓存穿透：用户想要查一个数据，发现Redis内存数据库中没有，也就是缓存没命中，于是向持久层数据库查询。发现也没有，于是本次查询失败。当用户很多时，缓存都没有命中（秒杀！），于是都去请求持久层数据库。这会导致持久层数据库造成很大的压力，这时候就相当于出现了缓存穿透。

> 解决方案

**布隆过滤器**

布隆过滤器是一种数据结构，对所有可能查询的参数以Hash形式存储，在控制层先进行校验，不符合则丢弃，从而避免存储系统的查询压力。

![img](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/cbbef657bf12b4e2d0d3a214b9008e46.png)

**缓存空对象**

当存储层不命中后，即使返回的空对象也将其缓存起来，同时会设置一个过期时间，之后再访问这个数据将会从缓存中获取，保护了后端数据源

![img](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/488f5f809f03a53dea1fdee1e9e41738.png)

**但是这个方法会存在两个问题**

- 如果空值能够被缓存起来，这就意味着缓存需要更多的空间存储更多的键，因为这当中可能会有很多的空值的键
- 即使空值设置了过期时间，还会存在缓存层和存储层的数据会有一段时间窗口的不一致，这对于需要保持一致性的业务会有影响

#### **缓存击穿(量太大,缓存过期!)**

> 概述

​         这里需要注意和缓存击穿的区别,缓存击穿,是指一个key非常热点,再不停的扛着大并发,大并发集中对这一个点进行访问,当这个key

在失效的瞬间,持续的大并发就穿破缓存,直接请求数据库,就像在一个屏障上凿开了一个洞.

当某个key在过期的瞬间,有大量的请求并发访问,这类数据一般是热点数据,由于缓存过期,会同时访问数据库来查询最新数据,并且回写缓存,

会导致数据库瞬间压力过大

> 解决方案

解决方案

**设置热点数据永不过期**

从缓存层面，没有设置过期时间，所以不会出现热点key过期后产生的问题。

**加互斥锁**

分布式锁：使用分布式锁，保证对于每个key同时只能有一个线程去查询后端服务，其他线程没有获得分布式锁的权限，因此只需要等待即可。这种方式将高并发的压力转移到了分布式锁，因此分布式锁的考验很大。


#### 缓存雪崩（集中失效）
缓存雪崩，是指在某一时间段，缓存集中过期失效。Redis宕机！

产生原因之一：

比如，双十一零点抢购，会把同一批商品信息比较集中的放入缓存中，假设缓存设置一个小时的过期时间，那么到凌晨一点钟的时候，这批商品的缓存就都过期了。而对这批商品的访问查询，都落到了数据库上，对于数据库而言，就会产生周期性的压抑波峰。于是所有的请求就会向存储层，存储层的调用量会暴增，可能造成存储层奔溃，服务器宕机。
![img](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/fe501710ac11f133173e4a4c8a6179ca.png)



其实集中过期，倒不是非常致命，比较致命的缓存雪崩，是缓存服务器某个结点宕机或断网。因为自然形成的缓存雪崩，一定是在某个时间段集中创建缓存，这个时候，数据库也是可以顶住压力的。无非就是对数据库产生周期性的压力而已。而缓存服务节点的宕机，对数据库服务器造成的压力是不可预知的，很可能瞬间就把数据库压垮。

解决方案

Redis高可用

这个思想含义是，既然Redis有可能挂到，那我们多增设几台Redis，这样挂掉之后其他的还可以继续工作，其实就是搭建集群(异地多活!)

限流降级

这个解决方案的思想是，在缓存失效后，通过加锁或队列来控制读数据库写缓存的线程数量。比如对某个key只允许一个线程查询数据和写缓存，其他线程等待。

数据预热

数据加热的含义就是在正式部署之前，我们先把数据先预先访问一遍，这样部分可能大量访问的数据就会加载到缓存中，在即将发生大并发访问手动触发加载缓存不同的key，设置不同的过期时间，让缓存失效的时间点尽量均匀。


# 扩展补充

### 命令行中如果不记得全部命令可以用**Tab**快捷键自动补全

### 相关文章

| 名称                                 | 地址                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| 4 种 Redis 集群方案介绍 + 优缺点对比 | [link](https://mp.weixin.qq.com/s?__biz=MzI3MjY1ODI2Ng==&mid=2247486094&idx=1&sn=f727b9fe6f53f4ebc5280dd09d6161ed&chksm=eb2e70bbdc59f9ad8157859f0c6eb5fa881c9e147d7e03ca9693633f7d2bff7215d0f813c847&scene=27) |

###  终端命令行运行redis客户端命令

> 终端输入**redis-cli**命令

```shell
C:\Users\铺先生技术研发中心>redis-cli
127.0.0.1:6379>
```















































































