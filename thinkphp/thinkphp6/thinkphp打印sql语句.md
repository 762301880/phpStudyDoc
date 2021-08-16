# 叙述

> 今天用thinkphp写项目的时候总是发现无法打印sql语句最终找到了解决方案			

# 代码示例

```shell
 # 使用buildSql()函数构建sql语句
 return Stu::where('id',2)->buildSql();
```

