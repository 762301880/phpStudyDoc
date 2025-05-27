# 说明

> 我们玩手机的时候经常会做一些重复的事情但是又不想自己手动的去做
>
> 例如朋友圈点赞,这时候就想着如果能有一个脚本帮我们完成这种没有意义
>
> 的工作那该多好

## 资料

| 名称                                | 地址                                         |
| ----------------------------------- | -------------------------------------------- |
| autojspro-官网                      | [link](https://pro.autojs.org/)              |
| api文档                             | [link](https://pro.autojs.org/docs/#/zh-cn/) |
| autojs-博客                         | [link](https://blog.autojs.org/)             |
| 阿里巴巴-矢量图库(可以用来下载图标) | [link](https://www.iconfont.cn/)             |

# 下载autojspro



| 名称                                   | 地址                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| 第三方软件园下载  对应自己保存外链下载 | [link](https://m.ddooo.com/softdown/172668.htm)   [link](https://yaoliuyang.lanzoui.com/iFI3awg65hg) |
|                                        |                                                              |

##  脚本实践

###  自动刷视频

```shell
auto.waitFor();
toast("开始刷视频");

function swipeUp() {
    let x = device.width / 2;
    let y_start = device.height * 0.75;
    let y_end = device.height * 0.35;
    let duration = random(300, 800);
    swipe(x, y_start, x, y_end, duration);
}

// 在新线程中执行刷视频逻辑
threads.start(function() {
    while (true) {
        swipeUp();
        let delay = random(5000, 15000); // 5~15秒
        toast("等待 " + Math.floor(delay / 1000) + " 秒...");
        sleep(delay);
    }
});

```

**自动刷视频脚本模拟真人**

```js
// 导入必要模块
auto.waitFor(); // 等待无障碍服务启动

// 设置随机数函数，用于生成8-15秒的随机等待时间
function randomTime(min, max) {
    return Math.floor(Math.random() * (max - min + 1)) + min;
}

// 设置随机数函数，用于生成随机滑动距离
function randomSwipeDistance() {
    return Math.floor(Math.random() * 300) + 400; // 生成400-700之间的随机数
}

// 设置随机数函数，用于生成随机概率
function randomChance(percent) {
    return Math.random() < percent;
}

// 获取屏幕尺寸
let deviceWidth = device.width;
let deviceHeight = device.height;

// 主函数
function main() {
    toast("开始自动刷视频");
    let videoCount = 0;
    
    while (true) {
        videoCount++;
        toast("正在观看第 " + videoCount + " 个视频");
        
        // 随机等待8-15秒
        let waitTime = randomTime(8, 15);
        log("等待时间: " + waitTime + " 秒");
        sleep(waitTime * 1000);
        
        // 随机执行点赞操作(10%的概率)
        if (randomChance(0.1)) {
            log("执行点赞操作");
            click(deviceWidth * 0.9, deviceHeight * 0.7);
            sleep(1000);
        }
        
        // 随机执行评论操作(5%的概率)
        if (randomChance(0.05)) {
            log("执行评论操作");
            click(deviceWidth * 0.5, deviceHeight * 0.85);
            sleep(1000);
            
            // 随机选择一条评论
            let comments = ["很不错", "这个视频太精彩了", "学到了很多", "666", "加油！"];
            let randomComment = comments[Math.floor(Math.random() * comments.length)];
            
            // 输入评论
            setText(randomComment);
            sleep(1000);
            
            // 发送评论
            click("发送");
            sleep(1000);
        }
        
        // 随机执行分享操作(3%的概率)
        if (randomChance(0.03)) {
            log("执行分享操作");
            click(deviceWidth * 0.85, deviceHeight * 0.8);
            sleep(1000);
            
            // 随机选择一个分享渠道
            click("复制链接");
            sleep(1000);
            
            // 返回
            back();
            sleep(1000);
        }
        
        // 随机滑动(90%的概率下滑，10%的概率上滑)
        if (randomChance(0.9)) {
            // 下滑到下一个视频
            log("下滑到下一个视频");
            swipe(deviceWidth * 0.5, deviceHeight * 0.8, deviceWidth * 0.5, deviceHeight * 0.2, randomTime(300, 800));
        } else {
            // 上滑查看更多内容
            log("上滑查看更多内容");
            swipe(deviceWidth * 0.5, deviceHeight * 0.2, deviceWidth * 0.5, deviceHeight * 0.8, randomTime(300, 800));
        }
        
        // 随机等待一段时间，模拟人类操作
        sleep(randomTime(1, 3) * 1000);
    }
}

// 启动主函数
main();
```

