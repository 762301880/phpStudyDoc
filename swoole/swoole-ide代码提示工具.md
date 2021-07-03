# 一、参考资料

| 项目名称            | 项目地址                                                     |
| ------------------- | ------------------------------------------------------------ |
| 第三方博客          | [跳转](https://blog.csdn.net/qq_37970180/article/details/103501759) |
| `Swoole IDE Helper` | [跳转](https://github.com/swoole/ide-helper)                 |
| `第三方文档`        | [跳转](https://toscode.gitee.com/JYPHP/swoole-ide-helper)    |
| github地址          | [跳转](https://github.com/wudi/swoole-ide-helper)            |

# 二、安装

 ## 2.1使用`composer`在项目中安装

```shell
composer require swoole/ide-helper:@dev
# 或者你可以安装一个特定的版本，比如:
composer require swoole/ide-helper:~4.4.7
```

- 开发环境安装

```shell
composer require --dev swoole/ide-helper:@dev
# 或者你可以安装一个特定的版本，比如:
composer require --dev swoole/ide-helper:~4.4.7
```

## 2.2 在`phpstorm`中添加外部库

### 2.2.1先将依赖下载到本地

```shell
composer require swoole/ide-helper:~4.4.7
```

![image-20210607094305230](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210607094305230.png)

### 2.2.2 添加下载包

![image-20210607094416140](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210607094416140.png)

# 使用

- 上诉安装完成之后，就可以直接在项目中使用`swoole`的语法了会自动的生成代码提示