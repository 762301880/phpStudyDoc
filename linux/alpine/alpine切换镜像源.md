## 资料

| 名称                    | 地址                                                         |
| ----------------------- | ------------------------------------------------------------ |
| 阿里云开源镜像站-alpine | [link](https://developer.aliyun.com/mirror/alpine?spm=a2c6h.13651102.0.0.3e221b11V02YoT) |

**说明**

> 我们知道常见的linux系统内置镜像源下载软件不是很少就是慢

### 配置方法

- 编辑 `/etc/apk/repositories`
- 将里面 `dl-cdn.alpinelinux.org` 的 改成 `mirrors.aliyun.com `; 保存退出即可

**使用命令替换示例**

```shell
cd /etc/apk && sed -i "s/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g" repositories   # 直接用sed命令一键替换
```

