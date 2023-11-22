#  说明



**资料**

| 名称                     | 地址                                                         |
| ------------------------ | ------------------------------------------------------------ |
| laravel_任务调度官方文档 | [link](https://learnku.com/docs/laravel/8.x/scheduling/9399) |





# 优化

# 如何在Laravel中实现每秒执行定时任务

参考

https://www.yisu.com/zixun/486965.html

## **实现-利用shell脚本实现秒级定时任务**

> 利用shell脚本实现

**编写shell脚本-seconds.sh**

```shell
#!/bin/bash
step=1 #间隔的秒数

for (( i = 0; i < 60; i=(i+step) )); do
   cd ~ && echo  $(date "+%Y-%m-%d %X") >> a.txt  # 需要执行的定时脚本-这里为了模拟写入文件执行时间
 sleep $step # 随眠定义的秒
done

exit 0
```





