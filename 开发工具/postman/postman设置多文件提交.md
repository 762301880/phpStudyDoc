# 说明

> 开发过程中我们会遇到提交多个文件一起上传的情况，
>
> 使用postman如何设置多文件上传呢

# postman设置

> 设置参数为数组类型，选择多个文件即可

![1632385363(1).jpg](https://i.loli.net/2021/09/23/JIZ1vBlPLqobHO9.png)

- php代码打印文件

```php
  dd($request->file('files')); #接收并打印上传的文件
```

