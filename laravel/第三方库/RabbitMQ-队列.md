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



