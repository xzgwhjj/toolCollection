<template>
	<view class="copy_song">
		<view class="copy_original_header">
			<text class="copy_original">原歌词文本</text>
			<view class="copy_clear" @tap="clear">
				<image :class="isClear ? 'cc_icon rotate-element' : 'cc_icon'" src="/static/images/icon_clear.png"
					mode="aspectFit"></image>
			</view>
		</view>
		<view class="copy_original_box">
			<view class="cob"></view>
			<view class="cob_box">
				<textarea v-model="songText" @input="onInput" @blur="bindTextAreaBlur" @confirm="bindTextAreaBlur"
					placeholder="请输入歌词" class="text_box" :maxlength='-1' />
			</view>
		</view>
		<view class="copy_original" style="margin: 20rpx 30rpx 0 30rpx;">生成文本</view>
		<scroll-view class="copy_scroll" scroll-y="true">
			<songList :text-lines="textLines" :current-index="currentIndex" @copySong="copySong"></songList>
		</scroll-view>
		<view class="copy_bottom">
			<songBottom :select-index="0"></songBottom>
		</view>
	</view>
</template>

<script setup>
	import {
		onMounted,
		ref
	} from 'vue';

	import {
		onLoad,
		onShow,
		onUnload,
		onReady,
		onShareAppMessage,
		onReachBottom,
		onPullDownRefresh
	} from '@dcloudio/uni-app'
	const app = getApp()

	//分享
	onShareAppMessage(() => {})

	let songText = ref('')

	function bindTextAreaBlur(e) {
		songText.value = e.detail.value
		if (songText.value != '' || songText.value != null) {
			processText()
		}
	}

	// 清空内容
	let isClear = ref(false)

	function clear() {
		isClear.value = true
		songText.value = ''
		textLines.value = []
		currentIndex.value = 0
		textObject = originObject
		let clearTimer = setTimeout(() => {
			isClear.value = false
			clearTimeout(clearTimer)
		}, 500)
	}

	let textLines = ref([])
	const originObject = {
		name: '',
		textList: [],
		currentIndex: 0,
		total: 0
	}
	let textObject = {}

	function processText() {
		if (!ifChange.value) return
		console.log("执行")
		textObject = originObject
		// 拆分文本为数组（按换行符分割）
		textLines.value = songText.value.split('\n')

		// 可选：过滤空行
		textLines.value = textLines.value.filter(line => line.trim() !== '')
		if (textLines.value.length > 0) {
			textObject.textList = textLines.value
			textObject.name = '歌曲' + app.globalData.dateTime()
			textObject.total = textLines.value.length
			console.log("textObject", textObject)
			console.log("historyList0", historyList)
			historyList.push(JSON.parse(JSON.stringify(textObject)))
			const oldSize = historyList.length
			console.log("historyList1", historyList)
			historyList = delSome(historyList)
			const newSize = historyList.length
			if (oldSize != newSize) {
				const itemToUpdate = historyList.find(item => JSON.stringify(item.textList) == JSON.stringify(
					textObject.textList))
				currentIndex.value = itemToUpdate.currentIndex
			}
			console.log("historyList2", historyList)
			uni.setStorageSync('historyList', historyList)
			ifChange.value = false
		}
	}
	// 去重
	// function delSome(historyList) {
	// 	const seenTextLists = new Set();
	// 	// 去重相同的子数组
	// 	return historyList.filter(item => {
	// 		const textListString = JSON.stringify(item.textList);
	// 		console.log("seenTextLists",seenTextLists)
	// 		if (!seenTextLists.has(textListString)) {
	// 			seenTextLists.add(textListString);
	// 			console.log("seenTextLists2",seenTextLists)
	// 			return true; // 保留该对象
	// 		}
	// 		return false; // 去掉该对象
	// 	});
	// }

	// 使用 reduce 和 Map 去重
	function delSome(array) {
		const map = new Map();
		// 转换为普通数组
		return array.reduce((acc, item) => {
			const textListString = JSON.stringify(item.textList);
			if (!map.has(textListString)) {
				map.set(textListString, true);
				acc.push(item);
			}
			return acc;
		}, []);
	}

	let ifChange = ref(false) // 输入框是否有输入删除
	function onInput(e) {
		if (e.detail.value == "") {
			ifChange.value = false
		} else {
			ifChange.value = true
		}
	}


	// 复制歌词
	let currentIndex = ref(0)

	function copySong(index) {
		currentIndex.value = index + 1
		const itemToUpdate = historyList.find(item => item.name == textObject.name)
		console.log("itemToUpdate", itemToUpdate)
		if (itemToUpdate) {
			itemToUpdate.currentIndex = currentIndex.value
		} else {
			const itemToUpdate = historyList.find(item => JSON.stringify(item.textList) == JSON.stringify(textObject
				.value.textList))
			console.log("itemToUpdate1", itemToUpdate)
			itemToUpdate.currentIndex = currentIndex.value
		}
		console.log(historyList);
		uni.setStorageSync('historyList', historyList)
	}

	let historyList = []
	onMounted(() => {
		if (uni.getStorageSync('historyList')) {
			historyList = uni.getStorageSync('historyList')
		}
	})
</script>

<style lang="less">
	.copy_song {
		width: 750rpx;
		max-height: 100vh;
		margin-top: 20rpx;
		padding: 20rpx 0;
		display: flex;
		flex-direction: column;
		justify-content: center;
		box-sizing: border-box;

		.copy_original_header {
			display: flex;
			justify-content: space-between;
			margin-bottom: 10rpx;
			padding: 0 30rpx;
			box-sizing: border-box;

			.copy_clear {
				width: 50rpx;
				height: 50rpx;
				border-radius: 16rpx;
				// background-color: #67c6f8;
				border: 2rpx solid rgba(0, 0, 0, .12);
				display: flex;
				justify-content: center;
				align-items: center;

				.cc_icon {
					width: 35rpx;
					height: 35rpx;
				}
			}
		}

		/* 定义动画 */
		@keyframes rotate360 {
			from {
				transform: rotate(0deg);
			}

			to {
				transform: rotate(360deg);
			}
		}

		/* 应用动画到元素 */
		.rotate-element {
			animation: rotate360 0.5s linear 1;
			/* 2秒完成一个周期，线性速度，只旋转一次 */
		}

		.copy_original_box {
			width: 650rpx;
			margin: 20rpx 50rpx;
			height: 350rpx;
			position: relative;
		}

		.cob_box {
			width: 100%;
			box-shadow: 0px 0px 12px rgba(0, 0, 0, .12);
			padding: 10rpx;
			box-sizing: border-box;
			border-radius: 16rpx;
			display: flex;
			justify-content: center;
			align-items: center;
			z-index: 10;
			position: absolute;
			top: 20rpx;
			left: 0;
			right: 0;
			background-color: #ffffff;
		}

		.cob {
			position: absolute;
			top: 0;
			width: 600rpx;
			height: 200rpx;
			left: 35rpx;
			right: 35rpx;
			background-color: #bdd2ef;
			border-radius: 32rpx;
			z-index: 5;
			box-shadow: 0px 0px 12px rgba(0, 0, 0, .12);
		}

		.copy_original {
			font-size: 36rpx;
			font-weight: bold;
			font-family: "阿里妈妈方圆体 VF Regular";
		}

		.copy_scroll {
			width: 750rpx;
			height: calc(100vh - 720rpx - env(safe-area-inset-bottom));
		}
	}

	.text_box {
		padding: 10rpx 0;
	}

	.copy_bottom {
		width: 670rpx;
		position: fixed;
		bottom: calc(20rpx + env(safe-area-inset-bottom));
		left: 40rpx;
		right: 40rpx;
	}

	/* 在线链接服务仅供平台体验和调试使用，平台不承诺服务的稳定性，企业客户需下载字体包自行发布使用并做好备份。 */
	@font-face {
		font-family: "阿里妈妈方圆体 VF Regular";
		src: url("//at.alicdn.com/wf/webfont/TsPsgzMM07VJ/3agsJts9kHAO.woff2") format("woff2"),
			url("//at.alicdn.com/wf/webfont/TsPsgzMM07VJ/ZA2jMP3PYK0o.woff") format("woff");
		font-display: swap;
	}
</style>