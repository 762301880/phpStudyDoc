## 网络博客

> https://cloud.tencent.com/developer/article/2480102

##  说明

> 因为 SSH 默认端口 22，很多网络限制这个端口，
>
> 需要 VPN 或者改用 `ssh -T -p 443 git@ssh.github.com`（GitHub 支持 443 端口 SSH）。

## 先做快速诊断（确定是哪层被挡）

在命令行逐条跑下面这些命令，观察输出（有错误就记下来）：

1. DNS 能否解析

```shell
nslookup github.com
# 或
dig +short github.com
```

1. HTTPS 是否通（这比 ping 更关键）

```shell
curl -v https://github.com/
curl -I https://api.github.com/   # 只看响应头
```

1. 测试 Git HTTPS 推送端口（443）能否连通

```shell
# mac / linux
nc -vz github.com 443
# windows (PowerShell)
Test-NetConnection github.com -Port 443
```

1. 测试 SSH（Git 的 SSH 服务）：

```shell
# 试默认 SSH（22）
ssh -T git@github.com -vvv
# 试 GitHub 提供的 443 端口 SSH（很多被墙网络用这个）
ssh -T -p 443 git@ssh.github.com -vvv
# 或检测端口连通性
nc -vz ssh.github.com 443
```

1. 用 Git 自带的调试输出看细节（推送一次测试）

```shell
GIT_TRACE=1 GIT_CURL_VERBOSE=1 git push origin main
```

把这些命令的关键错误或 `curl/ssh` 的 `verbose` 输出贴过来，能帮我准确判断是 DNS、端口、证书、还是运营商/企业防火墙问题。

##  解决方案



### 场景：SSH 被封（22 被屏蔽），但 443 可以用

- 使用 GitHub 提供的 `ssh.github.com:443`：
   在 `~/.ssh/config` 加一段：

```
Host github.com
  HostName ssh.github.com
  Port 443
  User git
  TCPKeepAlive yes
  IdentitiesOnly yes
  IdentityFile ~/.ssh/id_rsa
```

1. 修改 SSH 配置

在 **Windows 用户目录**（`C:\Users\你的用户名\`）下新建或编辑文件：

```
C:\Users\你的用户名\.ssh\config
```

写入：

```bash
Host github.com
  HostName ssh.github.com
  Port 443
  User git
  IdentityFile C:/Users/你的用户名/.ssh/id_rsa
  TCPKeepAlive yes
  IdentitiesOnly yes
```

⚠️ 注意：

- `IdentityFile` 路径要改成你本机的私钥路径（通常是 `C:/Users/你的用户名/.ssh/id_rsa`）。
- 如果你用的是 `ed25519` key，就写成 `id_ed25519`。

2. 测试 SSH 是否可用

保存后，在 Git Bash / PowerShell 里跑：

```
ssh -T git@github.com
```

如果一切正常，会看到类似：

```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```