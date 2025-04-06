<template>
	<view style="margin-top: 20rpx;padding: 20rpx 30rpx;">
		<search v-model:value="searchName" @searchResult="searchResult"></search>
		<text class="tip" v-if="reversedHistoryList.length > 0">长按可删除</text>
		<view class="history_item">
			<songList :song-type="2" :history-list="reversedHistoryList" @detailCopySong="detailCopySong"
				@delHistory="delHistory" @updateName="updateName">
			</songList>
		</view>
		<view class="history_bottom">
			<view class="copy_bottom">
				<songBottom :select-index="1"></songBottom>
			</view>
		</view>
	</view>
	<popup ref="dialogPopup" :type="'dialog'" :input-setting="inputSetting" @dialogInputConfirm="dialogInputConfirm">
	</popup>
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
		onHide,
		onPullDownRefresh,
		onReachBottom
	} from '@dcloudio/uni-app'

	// 跳转歌词复制页面
	function detailCopySong(item) {
		const newDetail = JSON.stringify(item)
		uni.navigateTo({
			url: `/pages/copySong/detailCopySong?detail=${encodeURIComponent(newDetail)}`
		})
	}

	// 长按删除
	function delHistory(item) {
		if (!item.name) {
			uni.showToast({
				title: '删除失败',
				icon: 'none'
			});
			return
		}
		historyList.value = historyList.value.filter(it => it.name !== item.name);
		uni.setStorageSync('historyList', historyList.value)
		uni.showToast({
			title: '删除成功',
			icon: 'none'
		});
		if (historyList.value.length == 0) {
			uni.navigateBack()
		}

	}

	let historyList = ref([])
	let reversedHistoryList = ref([])
	onShow(() => {
		if (uni.getStorageSync('historyList')) {
			historyList.value = uni.getStorageSync('historyList')
			searchResult()
		}
	})

	// 修改歌名
	let inputSetting = ref({
		title: '歌名修改',
		value: '',
		placeholder: '请输入歌名'
	})
	let dialogPopup = ref()
	let oldName = ref('')

	function updateName(name) {
		if (!name) return
		dialogPopup.value.popup.open('dialog')
		inputSetting.value.value = name
		oldName.value = name
	}

	function dialogInputConfirm(val) {
		console.log("val", val)
		if (val == '') {
			uni.showToast({
				title: '歌名不能为空',
				icon: 'none'
			})
			return
		} else if (isNameDuplicate(val)) {
			uni.showToast({
				title: '歌名不能重复',
				icon: 'none'
			})
			return
		}
		const itemToUpdate = historyList.value.find(item => item.name == oldName.value)
		console.log("itemToUpdate", itemToUpdate)
		if (itemToUpdate) {
			itemToUpdate.name = val
		}
		console.log(historyList.value);
		uni.setStorageSync('historyList', historyList.value)
		inputSetting.value.value = ''
		dialogPopup.value.popup.close()
		oldName.value = ''
	}

	// 检测歌名是否重复
	function isNameDuplicate(inputName) {
		return historyList.value.some(item => item.name === inputName);
	}

	// 根据歌名搜索
	let searchName = ref('')

	function searchResult() {
		if (searchName.value != '') {
			const searchList = filterByName(searchName.value)
			reversedHistoryList.value = searchList.slice().reverse();
			console.log("reversedHistoryList.value", reversedHistoryList.value)
		} else {
			reversedHistoryList.value = historyList.value.slice().reverse();
		}
	}


	// 函数：模糊查询
	function filterByName(inputName) {
		return historyList.value.filter(item => {
			// 检查 name 是否包含 inputName
			const nameMatch = item.name.includes(inputName);

			// 检查 textList 中是否有元素包含 inputName
			const textListMatch = item.textList.some(text => text.includes(inputName));

			// 返回 name 或 textList 中有匹配的对象
			return nameMatch || textListMatch;
		});
	}
	
	// 下拉刷新
	onPullDownRefresh(()=>{
		searchName.value = ''
		inputSetting.value.value = ''
		oldName.value = ''
		if (uni.getStorageSync('historyList')) {
			historyList.value = uni.getStorageSync('historyList')
			searchResult()
		}
		uni.stopPullDownRefresh()
	})
</script>

<style lang="less">
	.tip {
		color: #aaaaaa;
		font-size: 20rpx;
	}

	.history_item {
		padding-bottom: calc(20rpx + env(safe-area-inset-bottom) + 140rpx);
		box-sizing: border-box;
	}

	.text_box {
		border: 2rpx solid #aaaaaa;
		margin: 20rpx;
		padding: 10rpx;
	}

	.copy_text {
		border-bottom: 2rpx solid #aaaaaa;
		padding: 20rpx;
	}

	.history_bottom {
		width: 750rpx;
		position: fixed;
		bottom: 0;
		background-color: #ffffff;
		left: 0;
		right: 0;
		display: flex;
		justify-content: center;
		padding: 10rpx 40rpx calc(20rpx + env(safe-area-inset-bottom)) 40rpx;
		box-sizing: border-box;
		align-items: flex-end;

		.copy_bottom {
			width: 670rpx;
		}
	}
</style>