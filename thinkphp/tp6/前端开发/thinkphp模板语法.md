# 资料

| 名称                 | 地址                                                         |
| -------------------- | ------------------------------------------------------------ |
| thinkphp模板引擎语法 | [link](https://www.kancloud.cn/manual/think-template/1286403)  [link](https://www.kancloud.cn/manual/thinkphp6_0/1037613) |

## 分页

> 需要注意分页的个数一定要大于设置的值才会显示分页

- 后端代码

  ```php
   $stu= Stu::paginate(3);
   return  view('index',['stu'=>$stu]);
  ```

  

- 前端代码

```php+HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <!-- 引入 layui.css -->
    <link rel="stylesheet" href="//unpkg.com/layui@2.6.8/dist/css/layui.css">
    <!-- 引入 layui.js -->
    <script src="//unpkg.com/layui@2.6.8/dist/layui.js"></script>
    <!-- 新 Bootstrap 核心 CSS 文件 -->
    <link href="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">

    <!-- jQuery文件。务必在bootstrap.min.js 之前引入 -->
    <script src="https://cdn.staticfile.org/jquery/2.1.1/jquery.min.js"></script>

    <!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
    <script src="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/js/bootstrap.min.js"></script>
    <style>
        . pagination {
            list-style: none;
            margin: 0;
            padding: 0;
        }

        .pagination li {
            display: inline-block;
            padding: 20px;
        }
    </style>
</head>
<body>
<table class="layui-table">
    <thead>
    <tr>
        <th>name</th>
        <th>class_id</th>
        <th>birthday</th>
    </tr>
    </thead>
    <tbody>
    {volist name='stu' id='user'}
    <tr>
        <td>{$user->sname}</td>
        <td>{$user->class_id}</td>
        <td>{$user->birthday}</td>
    </tr>
    {/volist}

    </tbody>
</table>
{$stu|raw}
</body>

<!--<script>-->
<!--    layui.use('laypage', function () {-->
<!--        var laypage = layui.laypage;-->

<!--        //执行一个laypage实例-->
<!--        laypage.render({-->
<!--            elem: 'test1' //注意，这里的 test1 是 ID，不用加 # 号-->
<!--            , count: 50 //数据总数，从服务端得到-->
<!--        });-->
<!--    });-->
<!--</script>-->
</html>
```

