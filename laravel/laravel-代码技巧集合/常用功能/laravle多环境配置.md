#  本文参考博客

> https://learnku.com/articles/88612





```shell
// 项目根目录下，修改文件 bootstrap/app.php 如下：
$app = new Illuminate\Foundation\Application(
    $_ENV['APP_BASE_PATH'] ?? dirname(__DIR__)
);

$serverName = $_SERVER['SERVER_NAME'];

if (str_contains($serverName, 'local')) {
    $app->loadEnvironmentFrom('.env.local');
}if (str_contains($serverName, 'test')) {
    $app->loadEnvironmentFrom('.env.test');
}else {
    $app->loadEnvironmentFrom('.env');
}
//......
```

