# 说明

> 今天无意中查看

**资料**

| 名称        | 地址                               |
| ----------- | ---------------------------------- |
| 云redis官网 | [link](https://app.redislabs.com/) |
|             |                                    |

> 怎么配置的不详解就像你注册qq账号这么简单

![1646129429(1).jpg](https://s2.loli.net/2022/03/01/qhmbrvu21RAToQC.png)

## 项目中配置云reids

**laravel配置**

> 在**.env**中配置 端口还有密码

```shell
REDIS_HOST=redis-13822.c258.us-east-1-4.ec2.cloud.redislabs.com
REDIS_PASSWORD='********' # 自定义的密码
REDIS_PORT=13822
```

