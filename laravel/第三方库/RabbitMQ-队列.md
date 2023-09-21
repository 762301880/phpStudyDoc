#  说明

## [概述](https://baike.baidu.com/item/rabbitmq/9372144?fr=aladdin)

> RabbitMQ 是实现高级消息队列协议 (AMQP) 的开源消息代理软件（有时称为面向消息的中间件）。RabbitMQ 服务器是用 Erlang 编程语言编写的，并建立在 Open Telecom Platform 框架上，用于集群和故障转移。与代理交互的客户端库可用于所有主要编程语言。

# 资料

| 名称          | 地址                                       |
| ------------- | ------------------------------------------ |
| 第三方博客    | [link](https://learnku.com/articles/43080) |
| rabbitmq-官网 | [link](https://www.rabbitmq.com/)          |

# 安装

## docker安装RabbitMQ

**资料**

| 名称                     | 地址                                      |
| ------------------------ | ----------------------------------------- |
| docker-rabbitmq 官方镜像 | [link](https://hub.docker.com/_/rabbitmq) |

**安装**

```shell
docker pull rabbitmq    # 拉取镜像
docker run -itd --hostname my-rabbit  --name rabbit -p 15672:15672 -p 5673:5672 rabbitmq
```

# 实战

## laravel

### php-amqplib/php-amqplib 扩展包

**资料**

| 名称                                 | 地址                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| 第三方扩展包-php-amqplib/php-amqplib | [扩展包地址](https://packagist.org/packages/php-amqplib/php-amqplib)  [文档地址](http://php-amqplib.github.io/php-amqplib/namespaces/phpamqplib.html) |

#  黑马程序员MQ学习资料

**资料**

| 名称     | 地址                                                       |
| -------- | ---------------------------------------------------------- |
| 网络博客 | [link](https://www.ngui.cc/zz/2029459.html?action=onClick) |



## MQ的基本概念

###  MQ概述

> MQ全程**Message Queue**(消息队列),是在消息的传输过程中保存消息的容器,多用于分布式系统之间进行通信。

分布式系统子系统间两种通信方式

1. 远程调用

> A系统调用B系统

![在这里插入图片描述](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/20210326144550468.png)

2. 通过第三方传递消息

   > A系统先将消息发送给MQ然后MQ再将数据给B系统
   >
   > A系统是发送消息的我们称之为生产者  B系统接收消费消息的我们称之为消费者  那么中间的MQ我们称之为中间件

  ![在这里插入图片描述](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/20210326144651334.png)



 >**小结：**
 >**1、MQ，消息队列，存储消息的中间件；**
 >**2、分布式系统通信两种方式：直接远程调用 和 借助第三方 完成间接通信**
 >**3、发送方成为生产者，接收方称为消费者**

### MQ的优势和劣势

**优势**：

- 应用解耦
- 异步提速
-  削峰填谷

**劣势**：

- 系统可用性降低
- 系统复杂度提高
-  一致性问题

###  MQ的优势

1. ### 应用解耦

   > 订单系统直接调用别的系统
   >
   > 这张图可以这样来开  订单系统需要访问 库存系统  支付系统 物流系统  假如有一天我们需要增加一个X系统我们就需要再次对订单系统的业务进行修改
   >
   > 再比如我又新增一个Y系统我还要继续修改订单系统业务调用Y系统 假如有无数个系统呢

   ![在这里插入图片描述](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/20210326145831499.png)

系统的耦合性越高，容错性就越低，可维护性就越低。

- 通过MQ实现解耦

> 还是下单的操作只不过中间引入了一个MQ 当用户点击按钮下订单访问订单系统,那么订单系统只需要发送一条消息到MQ就可以了,这个时候就可以给用户返回说下单成功了

![在这里插入图片描述](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/20210326150410871.png)

使用MQ使得应用间解耦，提升容错性和可维护性

2. ### 异步提速

   - 远程调用方式：同步方式

> 当用户下完订单之后订单系统要保存自己的数据库花费20毫秒  通过远程调用的方式访问三个系统,这个时候是一种同步调用的方式
>
> 一共花费920毫秒

![在这里插入图片描述](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/20210326150618648.png)

一个下单操作耗时：20+300+300+300 = 920ms
用户点击完下单按钮后，需要等待920ms才能得到下单响应，太慢！

- MQ方式通信：异步

  > 当用户点击完毕按钮下订单访问订单系统 订单系统只需要保存数据库同时再像mq发送一个消息 然后就可以直接返回给用户订单执行成功了
  >
  > 后面的消费交给mq进行消费

![在这里插入图片描述](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/20210326150852924.png)

用户点击完下单按钮后，只需等待25ms就能得到下单响应（20+5 = 25ms）
提升用户体验和系统吞吐量（单位时间内处理请求的数目）。

3. **削峰填谷**

   - 远程调用

     > 比如说我们现在有个A系统 A系统接收用户的请求每秒最多处理1000个请求  那么假设现在A系统的产品经理说啊
     >
     > 需要做一个活动 网站人太少了引流 类似于一元强月饼秒杀活动  比如今天中午十二点一元抢月饼 很多用户得到消息时候
     >
     > 会蜂拥而至  那么A系统访问的流量瞬间就增多了

   ![在这里插入图片描述](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/20210326151838993.png)

​       请求瞬间增多，导致A系统压力过大而宕机

- MQ传递

  ![在这里插入图片描述](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/2021032615200161.png)



<img src="https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/20210326152049113.png" alt="在这里插入图片描述" style="zoom:80%;" />

使用了MQ之后，限制消费消息的速度为1000，这样一来，高峰期产生的数据势必会挤压在MQ中，高峰就被“削”掉了，但是因为消息积压，在高峰期过后的一段时间内，消费消息的速度还是会维持在1000，直到消费完积压的消息，这就叫做“填谷”。
使用MQ后，可以提高系统稳定性。

**小结：**
**1、应用解耦：提高系统容错性和可维护性**
**2、异步提速：提升用户体验和系统吞吐量**
**3、削峰填谷：提高系统稳定性。**

###  MQ的劣势

![在这里插入图片描述](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/20210326152739968.png)

- 系统可用性降低
  系统引入的外部依赖越多，系统稳定性越差。一旦MQ宕机，就会对业务造成影响。如何保证MQ的高可用？
- 系统复杂度提高
  MQ的加入大大增加了系统的复杂度，以前系统间时同步的远程调用，现在是通过MQ异步调用。如何保证消息没有被重复消
  费？怎样处理消息丢失情况？怎么保证消息传递的顺序性？
- 一致性问题
  A系统处理完业务，通过MQ给B、C、D三个系统发消息数据，如果B系统、C系统处理成功，D系统处理失败。如何保证消息数据处理的一致性？

**小结：**
**既然MQ有优势也有劣势，那么使用MQ需要满足什么条件呢？**
**① 生产者不需要从消费者出获得反馈。引入消息队列之前的直接调用，其接口的返回值应该为null，这才让明白下层的动作还没做，上层却当成动作做完了继续往后走，即所谓异步成为了可能。**
**②容许短暂的不一致性。**
**③确实用了有效果。即解耦、提速、削峰这些方面的收益，超过加入MQ，管理MQ这些成本**







