## abs 返回不同数的绝对值:(负数取整)

>  “ABS”是“Absolute”的缩写，意思是“绝对的”

**资料**

| 名称                              | 地址                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| 菜鸟教程-abs() 函数     php中文网 | [link](https://www.runoob.com/php/func-math-abs.html)  [link](https://www.php.cn/php-ask-487279.html) |

**示例**

```php
<?php

echo abs(-1).PHP_EOL;
echo abs(-7.5).PHP_EOL;
echo abs(9.9).PHP_EOL;
echo abs(8).PHP_EOL;

# 输出
1
7.5
9.9
8
```

[**php控制台输入输出**](https://blog.csdn.net/weixin_34377065/article/details/92143298?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-1-92143298-null-null.pc_agg_new_rank&utm_term=php+%E6%8E%A7%E5%88%B6%E5%8F%B0%E8%BE%93%E5%85%A5&spm=1000.2123.3001.4430)

```php
# echo.php
<?php
fwrite(STDOUT, "请输入消息："); 
$msg = trim(fgets(STDIN)); 
echo $msg;

# 终端使用
$ php echo.php
请输入消息：123
123
```

