## [资料](https://betheme.net/yidongkaifa/82934.html?action=onClick)





## 资料

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 博客     | [link](https://mp.weixin.qq.com/s?src=11&timestamp=1680311541&ver=4441&signature=iFCE42pyZRhiZjZxAzUw7j7OXTpznQ2eZAduCG-iSEzo5En4eOSTVbI2K5l7LWhMOL*jM4TpmnkKdGlsZjdD37749aLxPZMBrlwiw7Kcyl7AVvkmE0d0ewGmyRc2Y4w9&new=1)  [link](https://blog.51cto.com/u_15127685/3378085)  [link](https://betheme.net/yidongkaifa/82934.html?action=onClick)  [link](https://abcdabcd987.com/ssh/) |
| 备份文档 | [link](https://gitee.com/yaolliuyang/pdf_doc_backup/raw/master/SSH%20%E5%9F%BA%E6%9C%AC%E7%94%A8%E6%B3%95.pdf) |



# 端口转发

## 基本转发示例

> **-g：**允许远程主机连接到本地转发的端口，通常情况下，SSH在转发端口时，只允许本地主机的请求，使用-g参数可以使SSH允许远程主机的请求。需要注意的是，使用该参数存在一定的安全风险，应根据实际情况慎重决定是否使用。
>
> **-N：**表示不在远程主机上执行任何命令，通常在仅需要建立SSH隧道转发端口时使用该参数，而无需在远程主机上执行任何命令。
>
> **-L：**指定本地端口转发规则，格式为[本地地址:]本地端口:目标地址:目标端口，其中本地地址可选，如果不指定，则默认为localhost。该参数可以将本地端口转发至远程主机的指定端口，例如：
>
> **-f：**参数指定了在后台执行SSH连接(类似于后台守护进程)

```shell
ssh -g -N -L 7306:8.129.116.79:3306 -L 6379:8.129.116.79:6379  root@8.129.116.79  -p 21878
```



## 问题

### 那么如果远程端口例如远程服务器上的3306端口设置了只能本地服务器访问 我设置了代理转发无法连接怎么办

> 如果远程服务器的3306端口设置了只允许本地访问，那么使用SSH代理转发时，需要将远程主机指定为SSH服务器（即中转服务器），而不是直接指定远程服务器。
>
> 可以使用以下命令进行代理转发：

```shell
ssh -N -f -L 3306:localhost:3306 <SSH中转服务器的账号>@<SSH中转服务器的IP地址> -p <SSH中转服务器的端口>

# 例子
 ssh -g -N -L 7306:localhost:3306   root@8.129.116.79  -p 21878
```

**以上代码分析**

> 这条命令的作用是建立一个SSH隧道来实现远程端口转发。
>
> - ssh：表示要运行SSH客户端。
> - -N：表示不打开SSH远程命令，仅仅做端口转发。
> - -f：表示在后台运行SSH客户端。
> - -L 3306:localhost:3306：表示将本地主机的3306端口映射到SSH中转服务器上的3306端口，其中localhost是指SSH中转服务器的本地地址（127.0.0.1）。
> - <SSH中转服务器的账号>@<SSH中转服务器的IP地址>：表示SSH中转服务器的账号和IP地址。
> - -p <SSH中转服务器的端口>：表示SSH中转服务器的SSH端口号。
>
> 这条命令实现的效果是，在本地主机上，将本地3306端口的流量通过SSH隧道转发到SSH中转服务器上，并将数据流量再通过SSH中转服务器发送给远程MySQL服务器，即本地主机的MySQL客户端可以通过localhost:3306端口访问到SSH中转服务器上的MySQL服务。
>
> 需要注意的是，SSH中转服务器需要能够访问到远程MySQL服务器并且远程MySQL服务器的3306端口对SSH中转服务器是开放的，否则连接将失败。在实际应用中，需要根据实际情况对该命令进行修改才能满足特定的需求。