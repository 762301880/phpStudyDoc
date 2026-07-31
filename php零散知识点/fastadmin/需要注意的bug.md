##  插件配置被覆盖

例如  `vibetv\addons\alioss\config.php`路径

> 如果直接在测试服或者线上修改配置 只要本地提交了代码就会覆盖线上的配置
>
> 因为配置是保存在文件中的

##  git拉取代码导致插件无法编辑

> **原因:** **宝塔面板是www用户 登录的系统是root用户拉取代码给文件改为了root**

##  try catch 中不要用`$this->error('错误信息')`(超重要)

###  核心问题根源

1. `$this->error()` 底层逻辑

   

   FastAdmin 控制器`error()`方法：

   内部会直接抛出异常 / 终止当前请求、输出错误页面 / JSON 并 exit 退出程序，本质是中断执行的跳转方法，不是单纯返回提示文字。

**嵌套 try {} catch {} 场景矛盾**

你在 `try` 里调用 `$this->error()`：

执行到这一行就直接 `exit` 结束整个 PHP 进程，**外层 catch 永远捕获不到任何异常**，错误信息无法被接收、捕获、二次处理。

### 解决方案（抛出异常即可）

```php
$error = '';
try {
  $error = '数据保存失败';
        if ($error) {
            throw new \Exception($error);
        }   
    
} catch (\Exception $e) {
    $this->error($e->getMessage());
}
```

