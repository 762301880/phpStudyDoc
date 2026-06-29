



##  功能分析

参考 **淘宝  小红书   抖音的历史搜索功能**  

- 历史搜索最多只展示最新的二十条搜索(**新搜索头插、尾部淘汰**)
- 只要有新搜索默认插入到最前面 然后 默认往后排序到20   
- 本地localStorage + 云端存储
- 如果搜索的词存在前**20**条搜索历史中 提取到首条位置(**去重置顶**)
- 未登录 展示未登录的搜索  已登录展示已登录的搜索(用不同的key去区分登录key和未登录key)



## 参考图片

![5000字干货，帮你全方位解析搜索历史模块的设计 | 人人都是产品经理](https://image.woshipm.com/wp-files/2022/10/Js3twzdu3k8uPn79ZKSF.png)

### 细分场景

1. 种草 / 短视频（小红书、抖音、B 站）

   云端 Redis+MySQL 存 

   最多 100 条

    带时间戳完整记录

- 前端页面只展示最新 20 条；
- 跨设备同步时，后端下发全部云端记录，前端合并本地后再裁剪 20 条展示；
- 超过 100 条，后台自动删除**时间最久**的搜索词。

**电商平台（淘宝、京东）**

  云端上限 **80～100 条**，逻辑同上，电商搜索频次更高，上限略宽松。

**资讯 / 工具类（头条、夸克）**

轻量化，云端控制 **50 条**，节省存储成本。

### 冷热分层存储配套规则（大厂标配）

1. **Redis（热数据）**：只存最近 50～100 条高频搜索，毫秒查询；超量淘汰旧数据。
2. **MySQL 分库分表（持久层）**：同样限制单用户 100 条，定时任务清理老旧记录。
3. **数仓 ClickHouse/Hive**：全量搜索行为长期留存（6～12 个月，合规要求），**只用于离线分析，不给前端返回**。

## 结论

**不会只放前端，也不会每次都拉后端，是「本地缓存为主、云端持久同步为辅」混合架构**，小红书完全是这套标准设计。





## PHP + Redis 最简完整方案（直接能用）

### 不用建表！Redis 一条搞定

#### 新增 / 更新搜索历史（重复置顶、保留 20）

```php
$userId = 1001;
$keyword = trim($_POST['keyword']);
$key = "search_history:user_{$userId}";

$redis = new Redis();
$redis->connect('127.0.0.1', 6379);

// 1. 删除已存在的关键词（去重）
$redis->lRem($key, $keyword, 0);
// 2. 插到最前面（置顶）
$redis->lPush($key, $keyword);
// 3. 只保留最新20条，尾部淘汰
$redis->lTrim($key, 0, 19);
```

#### 获取搜索历史

```php
// 删除一条
$redis->lRem($key, $keyword, 0);
// 清空全部
$redis->del($key);
```

### 前端 localStorage 怎么配合

前端搜索时可以先存本地：

```php
function saveSearchKeyword(keyword) {
    let history = JSON.parse(localStorage.getItem('searchHistory') || '[]');

    history = history.filter(item => item !== keyword);
    history.unshift(keyword);
    history = history.slice(0, 20);

    localStorage.setItem('searchHistory', JSON.stringify(history));
}
```

## 实战代码

###  前端uniapp

**view**

```vue
	<!-- ========== 仿红果搜索历史模块（修复渲染） ========== -->
					<view class="search-history" v-if="historyList.length > 0">
						<view class="history-head">
							<view class="history-left">搜索历史</view>
							<view class="history-right">
								<!-- 默认状态：只显示删除图标 -->
								<u-icon v-if="!isEditHistory" name="close" size="36rpx" color="#aaa"
									@click="enterEdit" />
								<!-- 编辑状态：全部删除 | 完成 -->
								<template v-else>
									<text class="text-del-all" @click="clearAllHistory">全部删除</text>
									<text class="line">|</text>
									<text class="text-finish" @click="exitEdit">完成</text>
								</template>
							</view>
						</view>
						<!-- 只展示前6条 -->
						<view class="history-list">
							<view class="history-item" v-for="(word, index) in showHistoryList" :key="index"
								@click="tapHistoryWord(word)">
								<text class="word-text"
									:style="{color: is_night ? config.nightcolor : '#000'}">{{ word }}</text>
								<view @click.stop>
									<u-icon v-if="isEditHistory" name="close" size="32rpx" color="#999"
										@click="delSingleItem(index, word)" />
								</view>
							</view>
						</view>
					</view>
```

**js**

```js
                 keyword: '',
				list: [],
				show: true,
				historyList: [],
				isEditHistory: false

export default {
		computed: {
			searchKey() {  //封装key 不然每个方法写死key 太难受
				const token = uni.getStorageSync('token')
				const isLogin = !!token
				const prefix = isLogin ? 'searchHistory-':'searchHistoryNoAuth-'
				return prefix + this.current
			},
			showHistoryList() {
				return this.historyList.slice(0, 6)
			}
		},
```

**核心方法**

```js
saveSearchKeyword() {  //这个方法在需要写入的地方调用
				// 获取关键词并去空格
				const keyword = this.keyword.trim()
				if (!keyword) return

				let searchKey = this.searchKey
				// 读取缓存，不存在则为空数组
				let history = uni.getStorageSync(searchKey) || [];

				// 过滤掉相同旧关键词
				history = history.filter(item => item !== keyword);
				// 新关键词放最前面
				history.unshift(keyword);
				// 最多保留6条
				history = history.slice(0, 6);
				// 存入本地缓存
				uni.setStorageSync(searchKey, history);
				// 同步更新页面渲染列表
				this.historyList = history
			},
			// 进入编辑模式
			enterEdit() {
				this.isEditHistory = true
			},
			// 退出编辑模式
			exitEdit() {
				this.isEditHistory = false
			},
			// 清空当前分类全部历史
			clearAllHistory() {
				const key = this.searchKey
				uni.removeStorageSync(key)
				this.historyList = []
				this.isEditHistory = false
			},
			// 删除单条历史
			delSingleItem(index) {
				this.historyList.splice(index, 1)
				const key = this.searchKey
				uni.setStorageSync(key, this.historyList)
			},
			// 点击历史词条搜索
			tapHistoryWord(word) {
				this.keyword = word
				this.search()
			},
			async getRedisHistoryLogs() {
				const storageKey = this.searchKey
				// 读取本地存储
				let localList = uni.getStorageSync(storageKey) || []
				// 只取前6条
				localList = localList.slice(0, 6)

				// 本地有数据直接返回，不请求redis
				if (localList.length > 0) {
					this.historyList = localList
					return
				}

				// 本地为空，请求redis
				const res = await api.user.getSearchRedisLog({
					type: this.current
				})
				console.log(res, '--------------')
				const redisList = res.data || []
				// 覆盖本地存储 + 更新页面数据
				uni.setStorageSync(storageKey, redisList)
				this.historyList = redisList
			}
```

