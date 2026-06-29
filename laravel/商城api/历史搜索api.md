



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

**vue3**(直接复制即可)

```vue
<template>
	<view>
		<view container>
			<!-- 搜索框左右边距同步改成20rpx，和轮播对齐 -->
			<view class="search-box">
				<view class="search-inner">
					<!-- 点击搜索图标触发跳转 -->
					<uni-icons type="search" size="20" color="#5b403e" @click="search"></uni-icons>
					<!-- confirm 回车事件，v-model绑定输入值 -->
					<input v-model="searchVal" placeholder="搜索商品" class="search-input" @confirm="search" />
				</view>
			</view>

			<!--历史记录-->

			<view class="search-history" v-if="historyList && historyList.length > 0">
				<view class="history-head">
					<view class="history-left">搜索历史</view>
					<view class="history-right">
						<!-- 默认状态：只显示删除图标 -->
						<uni-icons v-if="!isEditHistory" type="close" size="36" color="#aaa" @click="enterEdit" />
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
					<view class="history-item" v-for="(word, index) in historyList" :key="index"
						@click="tapHistoryWord(word)">
						<text class="word-text">{{ word }}</text>
						<view @click.stop>
							<!-- 单条删除图标替换uni-icons -->
							<uni-icons v-if="isEditHistory" type="close" size="32" color="#999"
								@click="delSingleItem(index, word)" />
						</view>
					</view>
				</view>
			</view>


		</view>
	</view>
</template>

<script setup>
	import {
		ref,
		onMounted
	} from 'vue'

	const keyword = ref('') //传递过来的搜索参数
	// 搜索关键词
	const searchVal = ref('')

	//历史记录相关
	const list = ref([])
	const show = ref(true)
	const historyList = ref([])
	const isEditHistory = ref(false)
	const searchKey = ref('')

	onMounted(() => {
		const pages = getCurrentPages()

		// 兼容所有环境，不用at()
		const currentPage = pages[pages.length - 1]
		console.log('页面路径', currentPage.route)
		console.log('跳转参数', currentPage.options)
		const options = currentPage.options
		if (options && options.keyword) {
			try {
				keyword.value = decodeURIComponent(options.keyword)
			} catch (err) {
				keyword.value = options.keyword
			}
		}

		//
		setSearchKey()
		getRedisHistoryLogs()
	})


	const setSearchKey = () => { //封装key 不然每个方法写死key 太难受
		const token = uni.getStorageSync('token')
		const isLogin = !!token
		const prefix = isLogin ? 'searchHistory' : 'searchHistoryNoAuth'
		searchKey.value = prefix
		return prefix;
	}


	const search = () => {
		// 普通字符串，不是ref，不能加.value
		const searchText = searchVal.value.trim()
		if (!searchText) {
			uni.showToast({
				title: '请输入搜索内容',
				icon: 'none'
			})
			return
		}
		keyword.value = searchText
		saveSearchKeyword()
	}


	const saveSearchKeyword = () => { //这个方法在需要写入的地方调用
		// 获取关键词并去空格
		if (!keyword.value) return

		// 读取缓存，不存在则为空数组
		let history = uni.getStorageSync(searchKey.value) || [];

		// 过滤掉相同旧关键词
		history = history.filter(item => item !== keyword.value);
		// 新关键词放最前面
		history.unshift(keyword.value);
		// 最多保留6条
		history = history.slice(0, 6);
		// 存入本地缓存
		uni.setStorageSync(searchKey.value, history);
		// 同步更新页面渲染列表
		historyList.value = history
	}
	// 进入编辑模式
	const enterEdit = () => {
		isEditHistory.value = true
	}
	// 退出编辑模式
	const exitEdit = () => {
		isEditHistory.value = false
	}
	// 清空当前分类全部历史
	const clearAllHistory = () => {
		const key = searchKey.value
		uni.removeStorageSync(key)
		historyList.value = []
		isEditHistory.value = false
	}
	// 删除单条历史
	const delSingleItem = (index) => {
		historyList.value.splice(index, 1)
		const key = searchKey.value
		uni.setStorageSync(key, historyList.value)
	}
	// 点击历史词条搜索
	const tapHistoryWord = (word) => {
		keyword.value = word
		search()
	}
	const getRedisHistoryLogs = () => {

		// 读取本地存储
		let localList = uni.getStorageSync(searchKey.value) || []


		// 只取前6条
		localList = localList.slice(0, 6)

		// 本地有数据直接返回，不请求redis
		if (localList.length > 0) {
			historyList.value = localList
			return
		}

		// // 本地为空，请求redis-记得改为vue3写法
		// const res = await api.user.getSearchRedisLog({
		// 	type: this.current
		// })
		// console.log(res, '--------------')
		// const redisList = res.data || []
		// // 覆盖本地存储 + 更新页面数据
		// uni.setStorageSync(storageKey, redisList)
		// this.historyList = redisList
	}
</script>

<style scoped lang="scss">
	/* 搜索框左右padding改成20rpx，和轮播边距统一 */
	.search-box {
		padding: 8px 20rpx;

		.search-inner {
			display: flex;
			align-items: center;
			background-color: #f6f3f2;
			border-radius: 9999px;
			padding: 8px 16px;
			border: 1px solid #e4bebb30;

			.search-input {
				flex: 1;
				border: none;
				background: transparent;
				margin-left: 8px;
				font-size: 14px;
			}
		}
	}

	/* 搜索历史整体容器 */
	.search-history {
		margin: 10rpx 20rpx 0;
	}

	/* 历史头部：标题 + 操作按钮 */
	.history-head {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 16rpx 0;

		.history-left {
			font-size: 30rpx;
			color: #333;
			font-weight: 500;
		}

		.history-right {
			display: flex;
			align-items: center;
			gap: 16rpx;

			.text-del-all,
			.text-finish {
				font-size: 26rpx;
				color: #666;
			}

			.line {
				font-size: 26rpx;
				color: #ccc;
			}
		}
	}

	/* 历史词条流式布局 */
	.history-list {
		display: flex;
		flex-wrap: wrap;
		gap: 16rpx;
	}

	/* 单个历史标签 */
	.history-item {
		display: flex;
		align-items: center;
		gap: 12rpx;
		padding: 14rpx 24rpx;
		background-color: #f6f3f2;
		border-radius: 9999rpx;

		.word-text {
			font-size: 26rpx;
			color: #444;
		}
	}
</style>
```

