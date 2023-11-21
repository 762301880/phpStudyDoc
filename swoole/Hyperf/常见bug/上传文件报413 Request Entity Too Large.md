[^created_at]:2021/11/25
[^authot]:姚留洋

# 说明

> 昨天测试发现上传图片报了一个错误**413 Request Entity Too Large(请求实体太大)**,
>
> 参考解决方案的博客:	

```shell
# 在文件的server.php中添加 package_max_length
config\autoload\server.php
# package_max_length t
 'settings' => [
        Constant::OPTION_PACKAGE_MAX_LENGTH => 200 * 1024 * 1024, //添加上传的大小限制
    ],
```

