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
| DNS查询第三方网站 | [link](http://tool.chinaz.com/dns?type=1&host=github.com&ip=) [link](https://ipw.cn/dns/) |

#  修改ip

## windows 处理

**搜索dns-ttl值最小的ip地址**

<img src="https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/IKst3w7WNOzVpnQ.png" alt="1641860987(1).jpg" style="zoom:50%;" />

**修改虚拟域名访问**

> 记事本打开**C:\Windows\System32\drivers\etc\hosts**文件
>
> 在里面手动添加ip地址即可

```shell
# 手动添加的github dns
13.114.40.48   www.github.com	
```

# [方法二](https://zhuanlan.zhihu.com/p/272741266)

```php
sudo vi /etc/hosts  # linux hosts未知
    
C:\Windows\System32\drivers\etc    # windowsgithub位置
    
#添加    
# GitHub Start 
140.82.113.3      github.com
140.82.114.20     gist.github.com
151.101.184.133    assets-cdn.github.com
151.101.184.133    raw.githubusercontent.com
151.101.184.133    gist.githubusercontent.com
151.101.184.133    cloud.githubusercontent.com
151.101.184.133    camo.githubusercontent.com
151.101.184.133    avatars0.githubusercontent.com
199.232.68.133     avatars0.githubusercontent.com
199.232.28.133     avatars1.githubusercontent.com
151.101.184.133    avatars1.githubusercontent.com
151.101.184.133    avatars2.githubusercontent.com
199.232.28.133     avatars2.githubusercontent.com
151.101.184.133    avatars3.githubusercontent.com
199.232.68.133     avatars3.githubusercontent.com
151.101.184.133    avatars4.githubusercontent.com
199.232.68.133     avatars4.githubusercontent.com
151.101.184.133    avatars5.githubusercontent.com
199.232.68.133     avatars5.githubusercontent.com
151.101.184.133    avatars6.githubusercontent.com
199.232.68.133     avatars6.githubusercontent.com
151.101.184.133    avatars7.githubusercontent.com
199.232.68.133     avatars7.githubusercontent.com
151.101.184.133    avatars8.githubusercontent.com
199.232.68.133     avatars8.githubusercontent.com
# GitHub End
```

# bug 解析

## ssh connect to host github.com port 22 Connection timed out

**测试使用 linux服务器  ping github.com 可以ping通过**

```shell
root@hcss-ecs-739f:~# ping github.com
PING github.com (20.205.243.166) 56(84) bytes of data.
64 bytes from 20.205.243.166 (20.205.243.166): icmp_seq=1 ttl=109 time=83.9 ms
.....
^C
--- github.com ping statistics ---
13 packets transmitted, 13 received, 0% packet loss, time 12006ms
rtt min/avg/max/mdev = 83.890/83.903/83.938/0.012 ms

```

**公司电脑 ping 不通过 同事的电脑也试了**

解决方案 用手机数据线给电脑提供网络测试可以通过

