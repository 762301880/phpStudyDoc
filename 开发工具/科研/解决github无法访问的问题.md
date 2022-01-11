#  说明

> 今天早上想着github搜索点东西结果网站又打不开了,又懒得联vpn想着长此以往也不是个事,
>
> 万事不会问百度,搜索关键字**github有时候打不开怎么办2021**，以前看过原理就是说**github的dns**
>
> 被玩坏了所以很慢，那么修改一下dns所在地快一点的ip就可以了

## 参考资料

| 名称              | 地址                                                         |
| ----------------- | ------------------------------------------------------------ |
| 第三方博客参考    | [link](https://www.jianshu.com/p/5969b71548e0)               |
| DNS查询第三方网站 | [link](http://tool.chinaz.com/dns?type=1&host=github.com&ip=) |

#  修改ip

## windows 处理

**搜索dns-ttl值最小的ip地址**

<img src="https://s2.loli.net/2022/01/11/IKst3w7WNOzVpnQ.png" alt="1641860987(1).jpg" style="zoom:50%;" />

**修改虚拟域名访问**

> 记事本打开**C:\Windows\System32\drivers\etc\hosts**文件
>
> 在里面手动添加ip地址即可

```shell
# 手动添加的github dns
13.114.40.48   www.github.com
```

