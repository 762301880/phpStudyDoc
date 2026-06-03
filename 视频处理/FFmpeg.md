## 资料

| 名称           | 地址                                |
| -------------- | ----------------------------------- |
| FFmpeg 官网    | [官网](https://ffmpeg.org/)         |
| m3u8在线播放器 | [link](https://m3u8play.cn/player/) |

## Windows 安装步骤

1. 打开 `https://www.gyan.dev/ffmpeg/builds/` → 下载 **ffmpeg-release-full.7z**（完整版）
2. 解压到 `D:\ffmpeg`，把里面`bin`目录（含 ffmpeg.exe、ffprobe.exe）加入**系统环境变量 PATH**
3. 打开 CMD 输入：

```bash
ffmpeg -version
```

输出版本号即安装成功。

## Windows 检测 MP4 moov 是否在前（CMD 命令）

### 1、检测命令（cmd 直接运行，替换`test.mp4`为你的视频路径）

```bash
ffprobe -v trace -i "test.mp4" 2>NUL | findstr "type:'moov' type:'mdat'"
```

- **输出先出现 `type:'moov'` → moov 在头部（正常，流式播放）**
- **输出先出现 `type:'mdat'` → moov 在末尾（需要修复）**

### 2. 修复 moov 前置（不重新编码，秒处理）

```bash
ffmpeg -i "test.mp4" -c copy -movflags faststart "new_test.mp4"
```

生成的`new_test.mp4` moov 固定在文件开头。

## MP4 转 M3U8（Windows CMD 命令，2 种常用方案）

> 原理：MP4 切割成多个`.ts`分片 + 生成`.m3u8`索引文件
>
> [参考](https://ffmpeg.github.net.cn/ffmpeg-formats.html#hls-1)

### 方案 1：直接一键转码（通用推荐，自动 H264+AAC）

```bash
ffmpeg -i input.mp4 ^
-c:v libx264 -c:a aac ^
-hls_time 10 ^
-hls_list_size 0 ^
-f hls output.m3u8
```

参数说明

- `-hls_time 10`：每个 ts 分片**10 秒**（可改成 5/8/15）
- `-hls_list_size 0`：m3u8 保存全部分片（不自动删除旧片段）
- 执行后同目录生成：`output.m3u8` + `output0.ts、output1.ts...`

###  方案 2：无损快速切片（原视频已是 H264 编码，不二次转码速度最快）

```shell
ffmpeg -i .\001.mp4 -c copy -hls_time 6 -hls_list_size 0 output.m3u8
```



### 视频压缩

把**300M** 大视频压缩为 30M的1080P

```bash
ffmpeg -i output2.mp4 \
-c:v libx264 -preset fast -crf 23 \
-profile:v main -level 3.1 \
-c:a aac -b:a 128k -ar 44100 \
-g 30 -keyint_min 30 -sc_threshold 0 \
-bf 0 \
-hls_time 2 \
-hls_list_size 0 \
-hls_flags delete_segments+split_by_time \
-hls_playlist_type vod \
-hls_segment_filename output_%04d.ts \
-f hls output.m3u8
```









