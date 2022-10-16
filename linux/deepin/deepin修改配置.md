# [修改系统默认源](https://developer.aliyun.com/mirror/debian?spm=a2c6h.13651102.0.0.3e221b113ZXXth)

> 由于deepin使用的是**debian**内核所以我们需要先[查阅debian的版本](https://kzpu.com/archives/3071.html)

**资料**

| 名称                     | 地址                                                         |
| ------------------------ | ------------------------------------------------------------ |
| 阿里云-deepin 修改镜像源 | [link](https://developer.aliyun.com/mirror/deepin?spm=a2c6h.13651102.0.0.1e4d1b11YFc9Mn) |

## 配置方法

> 编辑`/etc/apt/sources.list`文件（需要使用**sudo**）, 在文件<font color="red">最前面</font>添加以下条目（操作前请做好相应备份）

```shell
deb [by-hash=force] https://mirrors.aliyun.com/deepin apricot main contrib non-free
```

