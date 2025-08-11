





# 补充

## cuid 卡 跟 ntag卡有什么区别

CUID 卡 和 NTAG 卡 都是常见的 RFID / NFC 卡类型，但它们的技术标准、芯片特性和应用领域都不一样。

我给你分成几个方面对比一下：

### 1. **芯片与协议标准**

| 对比项       | CUID 卡                                                      | NTAG 卡                                                      |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **本质**     | 一般是 **UID 可修改的 M1 卡（MIFARE 1K 或兼容卡）**，属于低成本的可复制卡 | NXP 出品的 NFC 芯片系列（NTAG203、NTAG213、NTAG215、NTAG216 等），UID 固化不可改 |
| **通信协议** | 遵循 **ISO/IEC 14443A**（同 MIFARE）                         | 遵循 **ISO/IEC 14443A**（同 NFC Type 2 Tag）                 |
| **内存结构** | 典型 1KB（16 扇区，每扇区 4 块，每块 16 字节）               | 容量从 144 字节到 888 字节（根据型号不同）                   |
| **UID**      | **可改写**（Clone 卡常用），原生 MIFARE 卡 UID 固化          | **只读 UID**（出厂烧录，无法更改）                           |



------

### 2. **安全性**

- **CUID 卡**：
  - 因为 UID 可改写，所以常被用来复制、模拟门禁卡，安全性低。
  - 常见在门禁破解、复制场景里。
  - 有的设备会检测卡的 `ATQA` / `SAK` 等参数来区分是不是克隆卡。
- **NTAG 卡**：
  - 原厂 UID 固化，不可改，防伪性更强。
  - 具备 NFC Forum Type 2 Tag 特性，可以做签名验证（部分型号有 NXP 原厂数字签名功能）。
  - 多用于支付、票务、产品溯源、NFC 名片、Amiibo 等。

------

### 3. **应用场景**

| 类型        | 常见用途                                       |
| ----------- | ---------------------------------------------- |
| **CUID 卡** | 门禁卡复制、停车场卡克隆、兼容旧设备           |
| **NTAG 卡** | NFC 贴纸、智能海报、Amiibo、手机配对、产品防伪 |



------

### 4. **价格与可得性**

- **CUID 卡**：便宜，一般几块钱一个（甚至更低），淘宝和电子市场很容易买到。
- **NTAG 卡**：价格稍贵（NTAG213 大概 2~5 元，NTAG215 稍贵，NTAG216 更贵）。

------

💡 **总结一句话**

- **CUID 卡** = 可改 UID 的 M1 克隆卡（安全性低，适合兼容/复制）
- **NTAG 卡** = NFC Type 2 正规卡，UID 固化，适合安全和防伪应用

------

如果你是想搞 **门禁复制**，那多半用的是 **CUID**；
 如果是做 **NFC 手机识别、Amiibo、智能标签**，那就是 **NTAG** 系列。

<img src="https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20250811110631551.png" alt="image-20250811110631551" style="zoom:50%;" />





## 卡的uid是什么意思

UID（Unique Identifier，唯一标识符）在 RFID / NFC 卡里，就像卡片的“身份证号码”，主要作用是**唯一标识这张卡**。

具体来说：

------

### 1. **身份识别**

- 当你刷门禁卡、公交卡、NFC 手机时，读卡器会先读取卡的 UID。
- 系统用这个 UID 去数据库里匹配权限或余额。
- 如果匹配上，就允许开门/扣费；匹配不上，就拒绝。

------

### 2. **防冲突**

- 同一时间如果有多张卡在感应区，读卡器通过 UID 区分它们，不会“混淆”。
- 协议里有一个“防冲突机制”（Anti-collision），就是基于 UID 来实现的。

------

### 3. **应用绑定**

- 有些系统只用 UID 做唯一标识，不关心卡内数据（比如简单的门禁、考勤机）。
- 也有些系统把 UID + 卡内数据一起用来验证（比如公交卡、银行卡）。

------

### 4. **安全性差异**

- **原厂卡 UID 固化** → 不可改（安全性高，用于支付、防伪、票务）
- **CUID / UID 可改卡** → UID 可以被修改（容易克隆，用于破解门禁）

------

💡 **简单比喻**：
 UID 就像车牌号，

- 车牌号固定 → 别人难冒充你（原厂 UID）
- 车牌号能随便换 → 容易有人冒充你（CUID 卡）

<img src="https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/raw" alt="img" style="zoom:50%;" />

# 实战

## 支付宝碰一碰写卡

> 支付宝送的碰一碰收款码太大了拿不下且随身携带不方便 所以打算把它弄小一点

**资料**

| 网络博客 | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 博客     | [link](https://www.bilibili.com/video/BV1ErquY4EbK/)  [link](https://post.cplus8.com/d/826) |

**操作**

> 读取标签可以找到支付宝**碰一碰的url** 直接写入**NTAG 256** nfc卡就行了

```shell
https://render.alipay.com/p/s/ulink/sn?s=dc&scheme=alipay%3A%2F%2Fnfc%2Fapp%3Fid%3D10000007%26actionType%3Droute%26codeContent%3Dhttps%253A%252F%252Fqr.alipay.com%252F2ts11160tfwy4ymolek4kf5%253FnoT%253Dnt00oxadg9hl
```



<img src="https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20250811111008268.png" alt="image-20250811111008268" style="zoom:25%;" />

<img src="https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20250811111026936.png" alt="image-20250811111026936" style="zoom:25%;" />