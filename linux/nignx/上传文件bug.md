# Nginx 413 Request Entity Too Large 完整解决

## 原因

你把**整本书几万章节的超大数组直接塞进队列任务**，请求体 / Redis 投递数据过大，Nginx 限制了客户端上传请求体大小，直接返回 413。

## 方案分两层：1. Nginx 配置放宽限制；2. 业务改造根治（推荐）

## 一、Nginx 修改配置（临时解决）

### 1. 修改站点 nginx.conf

```nginx
server {
    listen 80;
    server_name shturl.;

    # 核心：放宽请求体最大尺寸，根据你的小说大小设置
    client_max_body_size 100M;

    location / {
        proxy_pass http://127.0.0.1:9501;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

参数说明：

- `client_max_body_size 100M`：允许最大 100 兆请求体
- 若单本小说解析后章节内容合计 50M 就设 100M，200M 小说就设 300M

### 2. 重载 Nginx 生效

```bash
# 校验配置是否正确
nginx -t
# 重载
nginx -s reload
```

## 二、根治方案（必改，不推荐靠放大 nginx 硬扛）

### 问题根源

`chapter_list` 完整大数组丢进队列数据，请求 payload 暴增，哪怕开到 100M，多本同时上传依然会 413、Redis 内存暴涨、序列化慢。

### 改造思路：不传递完整章节数组，只传标识，队列内分页读取

1. 解析 txt 得到所有章节集合后，**批量入库临时表**，只存 book_id；
2. 队列只投递 `book_id` 数字，请求体极小，不会触发 413；
3. Job 消费时根据 book_id 分页从数据库取章节，分批上传 OSS；

### 代码改造示例

#### 1. 解析完成后批量入库，只推送 book_id 到队列

```php
$txtService = new TxtSplitService();
$chapterCollection = $txtService->split($filePath);

// 生成唯一书籍任务ID
$bookTaskId = uniqid('book_upload_');
// 批量插入临时章节表
$insertData = [];
$chapterNo = 1;
foreach ($chapterCollection as $item) {
    $insertData[] = [
        'task_id' => $bookTaskId,
        'title' => $item['title'],
        'content' => $item['content'],
        'chapter_no' => $chapterNo
    ];
    $chapterNo++;
}
// 批量插入，分批次插入防止mysql包过大
Db::name('book_temp_chapter')->chunkInsert($insertData, 100);

// 队列只传task_id，数据极小，不会413
Queue::push(BookOssUploadJob::class, [
    'task_id' => $bookTaskId,
    'save_dir' => 'book'
]);

// 释放大数组内存
unset($chapterCollection, $insertData);
gc_collect_cycles();
```

#### 2. Job 中分页读取数据库，分批上传

```php
public function fire(Job $job, $data)
{
    $taskId = $data['task_id'];
    $saveDir = $data['save_dir'];
    $ossService = new OssService();
    $batchSize = 50;
    $page = 1;

    while (true) {
        // 分页取出一批章节
        $list = Db::name('book_temp_chapter')
            ->where('task_id', $taskId)
            ->page($page, $batchSize)
            ->select()
            ->toArray();

        if (empty($list)) break;

        foreach ($list as $item) {
            try {
                $ossService->uploadContent($item['content'], $item['chapter_no'] . '.txt', $saveDir);
            } catch (\Exception $e) {
                trace("章节上传失败：{$item['title']} " . $e->getMessage(), 'error');
            }
        }
        usleep(80000);
        $page++;
        gc_collect_cycles();
    }

    // 全部上传完成，清理临时表数据
    Db::name('book_temp_chapter')->where('task_id', $taskId)->delete();
    $job->delete();
}
```

## 三、补充配套优化（避免其他地方 413）

### 1. PHP-FPM 上传限制同步放大（nginx 改了 php 也要配合）

`php-fpm.conf` 或对应 pool 配置：

```ini
php_admin_value[post_max_size] = 100M
php_admin_value[upload_max_filesize] = 100M
```

重启 php-fpm：

```bash
systemctl restart php-fpm
```

### 2. ThinkPHP 队列 Redis 序列化过大优化

若坚持传递大数组到队列，可关闭自动序列化压缩，或拆分任务；但最优方案仍是入库只传 ID。

## 总结

1. 临时解决：Nginx `client_max_body_size 100M` + PHP 上传参数同步放大；
2. 长期根治：章节内容存入临时数据库，队列仅传递任务 ID，彻底消除超大请求体，不再出现 413，同时减少 Redis 内存占用。