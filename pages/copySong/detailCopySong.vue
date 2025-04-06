<template>
	<view style="margin-top: 20rpx;padding: 20rpx;">
		<songList :text-lines="detailSong.textList" :current-index="currentIndex" @copySong="copySong"></songList>
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

	let detailSong = ref([])
	onLoad((e) => {
		detailSong.value = JSON.parse(decodeURIComponent(e.detail))
		console.log("detailSong.value", detailSong.value)
		currentIndex.value = detailSong.value.currentIndex
	})


	// 复制歌词
	let currentIndex = ref(0)

	function copySong(index) {
		currentIndex.value = index + 1
		let historyList = uni.getStorageSync('historyList')
		const itemToUpdate = historyList.find(item => item.name == detailSong.value.name)
		console.log("itemToUpdate", itemToUpdate)
		if (itemToUpdate) {
			itemToUpdate.currentIndex = currentIndex.value
		}
		console.log(historyList);
		uni.setStorageSync('historyList', historyList)
	}
</script>

<style lang="less">
	.text_box {
		border: 2rpx solid #aaaaaa;
		margin: 20rpx;
		padding: 10rpx;
	}

	.copy_text {
		border-bottom: 2rpx solid #aaaaaa;
		padding: 20rpx;
	}
</style>