# 项目中如何调试swoole

## 使用echo 输出

> 例如你在swoole中使用**echo** 进行调试语句输出, **cli**启动swoole 后就可以在控制台看到输出的调试语句
>
> **假如你是supervisor中启动  你就要在supervisor配置的日志文件中查看输出** 

```php
    public function WorkerStart($server, $worker_id)
    {
        echo('WorkerStart' . $worker_id).PHP_EOL;//cli启动swoole调试使用
     }

```

