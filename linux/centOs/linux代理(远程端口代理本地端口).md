# 利用ssh代理访问远程linux服务器上的服务（例如数据库）

## 说明

> 以前开发的时候是有运维给我们配置开发对应的测试服务器,直接通过**ip地址+端口地址+用户名+密码**访问对应的服务
>
> 即可，新入职了一家公司是通过代理访问的关于代理我目前还不是很理解(它们自己编写了一个服务可以让我们通过打开代理)去访问
>
> 对应的mysql 或者redis服务，感觉用的不是很舒服，网上查了一下有对应的ssh代理软件可以挂后台的那种所以拿过来用
>
> **为什么要这样做**
>
> 因为有的服务器端口不会对外(远程)开放，只能服务器内部本地访问端口,但是我们可以链接服务器权限的时候，
>
> 我们就能通过代理转发的方式在本地访问到其上的服务

## 资料

| 名称                            | 地址                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| **Bitvise SSH Client** 下载地址 | [个人网盘](https://yaoliuyang.lanzoul.com/iJlYY01wa6fa)  [link](https://tunnelier.en.softonic.com/?ex=CAT-3508.5) |
| 参考资料                        | [link](https://blog.csdn.net/qq_16320025/article/details/103962044)   [link](https://www.cnblogs.com/yu29/p/10332891.html)  [link](https://blog.csdn.net/weixin_43860781/article/details/104898684) [link](https://www.likecs.com/show-204714390.html) |

## **配置**

### 启动软件，在Login配置窗口下配置建立ssh连接信息：红框中选择password

![1648004381(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/cXHnrdiUNL6oyWT.png)

### 配置本地端口映射：

> **list.port** 填写自己本地的端口  例如**7306** 是我本地映射的端口 项目中需要用7306连接

![1648004523(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/GtqTnKy3wSd9L6M.png)

## **坑记录**

### 映射端口与本地服务端口重复问题

> 例如上图 如果你想用**6379**访问服务器上面的**6379** redis服务，记得把本地的reids切换端口或者关闭本地端口，再
>
> 或者换一个链接端口
>
> 补充:实际上一个本地端口代理远程3306端口就行了因为数据库不一样 密码不一样  所以连接的虽然是同一个端口
>
> 但是访问的确实不同的数据库





#  利用服务器实现内网穿透(本地端口代理到远程服务器)

> 内网穿透，他们有一个公网的Linux服务器，想要在本地环境利用这个服务器实现内网穿透。首先，我得理解他们的需求。内网穿透通常用于让外部网络访问到内网中的服务，比如本地开发的Web应用或者远程桌面。用户可能是在开发过程中需要让外部访问本地服务，或者有设备在内网需要远程访问。
>
> 首先，我需要确认他们是否已经了解内网穿透的基本概念，或者是否需要进一步解释。不过用户已经知道内网穿透软件，所以可能不需要基础知识的讲解。接下来，我需要考虑他们是否有特定的软件偏好，或者是否需要推荐几种常见的工具。常见的工具有frp、ngrok、NPS、ssh反向隧道等。
>
> 然后，用户提到他们有一个公网Linux服务器，这很重要，因为内网穿透通常需要一个具有公网IP的服务器作为中转。用户可能希望在这个服务器上搭建服务，将流量转发到本地环境。接下来，我需要考虑如何选择合适的工具，并指导他们进行配置。
>
> 可能要考虑用户的技术水平。如果他们比较熟悉命令行和网络配置，推荐使用frp或者ssh反向隧道可能更合适。如果希望快速搭建，ngrok可能更方便，但可能需要注册账户，或者自己搭建ngrok服务端，而frp和NPS都是开源的自托管解决方案，适合有自己服务器的用户。
>
> 另外，安全性也是一个需要考虑的因素。使用内网穿透时，需要确保传输的数据是加密的，防止中间人攻击。比如，ssh隧道本身是加密的，而frp也支持TLS加密。用户可能需要指导如何配置这些安全选项。
>
> 接下来，我应该分步骤给出几种常见的方法，并简要说明每种方法的优缺点，让用户可以根据自己的情况选择。例如，ssh反向隧道不需要额外安装软件，但可能不够灵活；frp需要安装服务器端和客户端，但功能更强大；ngrok可能更简单，但需要依赖第三方服务或自己搭建服务器。
>
> 还需要考虑端口的使用和防火墙设置。用户需要确保公网服务器的防火墙开放了相应的端口，比如frp的服务器端端口，或者ssh隧道的端口。此外，可能需要配置域名解析，将域名指向公网服务器的IP，并配置子域名或路径转发。另外，用户可能遇到的问题包括连接不稳定、配置错误、权限问题等。需要提醒他们在配置过程中检查日志，确保服务正常运行，并且服务器和客户端的配置一致最后，可能需要给出一些常见问题的解决方法，或者推荐进一步阅读的文档链接，帮助用户深入理解配置过程。

## **方案一：使用 `frp`（推荐）**

### 资料

| 名称                             | 地址                                                |
| -------------------------------- | --------------------------------------------------- |
| frp中文文档                      | [link](https://gofrp.org/zh-cn/docs/)               |
| docker-frp安装地址(需要翻墙访问) | [link](https://hub.docker.com/r/snowdreamtech/frps) |
| github服务端&客户端地址          | [link](https://github.com/fatedier/frp)             |
| 网络博客                         | [link](https://www.cnblogs.com/geek233/p/18791892)  |

一款高性能的反向代理工具，适合自建内网穿透服务。

### 安装

#### 1. docker安装frps服务端(推荐)

```bash
docker run --restart=always --network host -d -v /etc/frp/frps.toml:/etc/frp/frps.toml --name frps snowdreamtech/frps
```

**--network host 解释**

> Docker的网络模式。Docker有不同的网络模式，比如桥接模式、主机模式、无网络等。主机模式（host）是其中一种，它允许容器共享宿主机的网络命名空间。这意味着容器不会拥有自己独立的网络栈，而是直接使用宿主机的网络接口和端口。
>
> 接下来，用户给出的命令是运行一个FRP服务器（frps），这可能用于内网穿透或者反向代理。FRP通常需要监听特定的端口，比如7000或者其他配置的端口。使用主机网络模式的话，容器中的应用程序可以直接绑定到宿主机的端口，不需要通过Docker的端口映射，这样可以简化配置，特别是在需要监听多个端口或者动态端口时比较方便。
>
> 然后，我需要考虑主机模式带来的影响。例如，容器不会有自己的IP地址，所有网络活动都直接通过宿主机。这可能对安全性有影响，因为容器和宿主机共享网络，容器内的服务可能会暴露更多的端口到外部。此外，如果宿主机有防火墙或者其他网络策略，可能需要调整来允许FRP的工作。
>
> 用户可能想知道为什么在这里使用主机模式。可能的答案是，FRPS需要监听多个端口，或者用户希望避免在Docker命令中逐个映射端口。例如，在FRPS的配置文件中可能指定了多个端口，使用主机模式可以让容器直接使用这些端口而无需额外的映射，简化了配置步骤。
>
> 另外，我需要确认主机模式是否适用于所有情况。例如，在Docker的不同环境中，比如在Windows或Mac上使用Docker Desktop时，主机模式的行为可能有所不同，但在Linux环境下，主机模式是直接使用宿主机的网络。因此，用户可能是在Linux宿主机上运行这个命令，以确保FRPS能够正确绑定端口。
>
> 还有可能存在的误区是，用户可能认为使用主机网络模式会导致性能问题，但实际上这可能对性能影响不大，反而在需要高性能网络的应用中，主机模式可能减少网络转换的开销。
>
> 最后，总结一下，`--network host`的作用是让容器使用宿主机的网络，省去端口映射的麻烦，适合需要直接访问宿主网络的情况，比如运行需要监听多个端口的服务如FRPS。

在 Docker 命令中，`--network host` 表示容器将直接使用宿主机的网络命名空间（Network Namespace），而不是创建一个独立的虚拟网络环境。这意味着：

1. **网络栈共享**

- 容器不再拥有独立的 IP 地址、端口和路由表。
- 容器内的应用程序会**直接绑定到宿主机的网络接口**，例如：
  - 如果容器内的服务监听 `0.0.0.0:8080`，则等同于宿主机直接监听 `0.0.0.0:8080`。
  - 无需通过 `-p 8080:8080` 手动映射端口，所有端口自动“暴露”。

2. **应用场景（以 `frps` 为例）**

- **FRP 服务端（frps）** 通常需要对外暴露多个端口（如控制端口、数据传输端口等）。
- 使用 `--network host` 可避免逐个映射端口，简化配置，例如：

```toml
# frps.toml 配置示例
bindPort = 7000  # 直接使用宿主机的 7000 端口
```

- 特别适合需要动态或大量端口的服务（如 NAT 穿透、反向代理）。

3. **与默认桥接模式（Bridge）的对比**

| 特性           | `--network host`                     | 默认桥接模式                         |
| :------------- | :----------------------------------- | :----------------------------------- |
| **网络隔离性** | 无隔离，共享宿主机网络               | 独立网络栈，隔离性强                 |
| **端口映射**   | 无需 `-p` 参数，直接使用宿主机端口   | 需通过 `-p 宿主机端口:容器端口` 映射 |
| **性能**       | 略高（无 NAT 转换开销）              | 略低（存在 NAT 转换）                |
| **安全性**     | 较低（容器服务直接暴露在宿主机网络） | 较高（可通过防火墙规则限制访问）     |

4. **注意事项**

- **权限问题**：某些操作（如绑定低端口号 `<1024`）可能需要 `root` 权限，需确保容器有足够权限（例如添加 `--cap-add=NET_ADMIN`）。
- **端口冲突**：若宿主机已占用某个端口（如 `7000`），容器内的服务会启动失败。
- **适用环境**：在 Linux 宿主机的 Docker 中效果符合预期；在 macOS/Windows 的 Docker Desktop 中，`host` 模式可能表现不同（实际是虚拟机网络）。

5. **你的命令解析**

bash

复制

```
docker run --restart=always --network host -d \
  -v /etc/frp/frps.toml:/etc/frp/frps.toml \
  --name frps snowdreamtech/frps
```

- `frps` 通过宿主机的网络直接暴露端口，配置文件中的端口（如 `bindPort`）会直接绑定到宿主机。
- 适合需要简化端口管理或涉及大量动态端口的 FRP 场景。

如果需要更强的隔离性，可以改用桥接模式并显式映射端口：

```bash
docker run --restart=always -d \
  -p 7000:7000 \
  -p 7500:7500 \
  -v /etc/frp/frps.toml:/etc/frp/frps.toml \
  --name frps snowdreamtech/frps
```





#### 2. **配置** 

```bash
# frps.toml 
[common]
bind_port = 7000  # 客户端与服务器通信的端口
token = your_secure_token         # 建议设置认证密钥
dashboard_port = 7500  # 管理面板端口（可选）
dashboard_user = admin   # 管理面板用户名
dashboard_pwd = admin  # 管理面板密码


# 命令创建
# 创建 frps 配置文件目录
mkdir -p /etc/frp && cd /etc/frp
# 创建 frps.ini 配置文件
cat > frps.toml << EOF
[common]
bind_port = 7000
token = your_secure_token
dashboard_port = 7500
dashboard_user = admin
dashboard_pwd = admin
EOF
```

###  frps客户端

#### windows端

> https://www.kobin.cn/frp/

![image-20250414105435513](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20250414105435513.png)

## **方案二：`ngrok` 的 Docker 部署**

### **自建服务端（可选）**

```bash
# 1. 克隆 ngrok 官方仓库
git clone https://github.com/inconshreveable/ngrok.git
cd ngrok

# 2. 生成 TLS 证书（需替换域名）
openssl genrsa -out rootCA.key 2048
openssl req -x509 -new -nodes -key rootCA.key -days 5000 -out rootCA.pem -subj "/CN=ngrok.yourdomain.com"

# 3. 构建 Docker 镜像
docker build -t ngrok-server .

# 4. 运行服务端
docker run -d \
  -p 4443:4443 \
  -p 80:80 \
  -p 443:443 \
  --name ngrokd \
  ngrok-server /bin/sh -c "/ngrok/bin/ngrokd -domain=ngrok.yourdomain.com -httpAddr=:80 -httpsAddr=:443"
```

### **客户端连接**

```bash
# 本地机器运行客户端（需替换域名和端口）
docker run --net=host \
  -it ngrok/ngrok \
  http 8080 -subdomain=test -hostname=ngrok.yourdomain.com:4443
```

## **方案三：`NPS` 的 Docker 部署**

### **服务端（公网服务器）**

```bash
# 创建配置文件目录
mkdir -p /opt/nps/conf

# 运行服务端容器
docker run -d \
  --name nps \
  --restart always \
  -p 80:80 \
  -p 8024:8024 \
  -p 8080:8080 \
  -v /opt/nps/conf:/conf \
  ffdfgdfg/nps
```

### **客户端（本地机器）**

```bash
# 在 NPS 管理界面生成客户端连接命令（格式如下）
docker run -d \
  --name npc \
  --restart always \
  -e SERVER=服务器IP:8024 \
  -e VKEY=客户端密钥 \
  ffdfgdfg/npc
```

## **方案四：`SSH 反向隧道` 的 Docker 化**

### **本地机器通过容器建立隧道**

```bash
# 将本地 80 端口通过容器映射到服务器的 8000 端口
docker run -d \
  --name ssh-tunnel \
  --network host \  # 使用宿主机网络模式
  alpine/openssh \
  ssh -NfR 8000:localhost:80 root@服务器IP -o "StrictHostKeyChecking=no" -o "ServerAliveInterval=60"
```

## **关键注意事项**

1. **网络模式**：
   - 若需暴露本地服务，客户端容器建议使用 `--network host` 模式（直接共享宿主机网络）。
   - 服务端需确保 Docker 映射端口与配置文件一致（如 `-p 7000:7000`）。
2. **配置持久化**：
   - 通过 `-v` 将配置文件挂载到容器内（如 `-v /opt/frp/frps.ini:/etc/frp/frps.ini`）。
3. **安全性**：
   - 务必修改默认的 `token`、`dashboard_pwd` 等敏感信息。
   - 限制服务端暴露的端口范围（避免开放不必要的端口）。

### **推荐选择**

- **快速部署**：直接使用 `snowdreamtech/frps` 或 `ffdfgdfg/nps` 官方镜像。
- **轻量级测试**：SSH 隧道 + Docker 临时容器。
- **企业级需求**：结合 Docker Compose 编排多节点穿透服务。

按上述步骤操作，即可通过 Docker 快速实现内网穿透，无需手动编译或管理依赖环境！



#  补充

## 免费云端搭建frp

地址

> https://run.claw.cloud/
>
> https://docs.run.claw.cloud/         文档

https://gitee.com/yao_liuyang/pdf_doc_backup/raw/master/claw.cloud%E4%BD%BF%E7%94%A8%E6%96%87%E6%A1%A3.docx 



