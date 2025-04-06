<template>
	<view class="box-bg">
		<view @tap="gotoSong" class="box_item">
			<image class="bi_icon" src="/static/images/icon_song.png" mode="aspectFit"></image>
			<text>复制歌词</text>
			<image class="bi_tip" src="/static/images/icon_tip.png" mode="aspectFit" @tap.stop="getTip"></image>
		</view>
	</view>
	<!-- 复制歌词 -->
	<popup ref="songPopup" :select-list="songList" @gotoSelect="gotoSongSelect"></popup>
	<popup ref="songZeroPopup" :type="'center'" :use-type="'video'">
		<template #video>
			<video id="songVideo" class="video_content" :src="videoSongSetting.src"
				:autoplay="videoSongSetting.autoplay" controls></video>
		</template>
	</popup>
	<popup ref="songOnePopup" :type="'center'" :dialog-content="songContent" :dialog-confirm="'知道了'"
		:is-mask-click="false" @confirm="songConfirm"></popup>
</template>

<script setup>
	import {
		nextTick,
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

	function gotoSong() {
		app.globalData.isOneEnter(() => {
			uni.navigateTo({
				url: '/pages/copySong/copySong',
			})
		})
	}

	/**
	 * 歌词复制
	 */
	let songPopup = ref()
	let songList = ref([{
		value: 0,
		label: '视频介绍'
	}, {
		value: 1,
		label: '文字介绍'
	}, ])

	function getTip() {
		console.log("songPopup", songPopup.value)
		songPopup.value.popup.open('bottom')
	}

	function gotoSongSelect(index) {
		if (index == 0) {
			songSelectZero()
		} else if (index == 1) {
			songSelectOne()
		}
	}

	// 视频介绍
	let videoSongSetting = ref({
		src: '/static/video/none_video.mp4',
		autoplay: false
	})
	let songZeroPopup = ref()
	let videoRef = ref()

	function songSelectZero() {
		if (videoSongSetting.value.src == '') {
			uni.showToast({
				title: '暂无视频介绍'
			})
			return
		}
		songZeroPopup.value.popup.open('center')
		// videoSongSetting.value.autoplay = true
		const videoContext = uni.createVideoContext('songVideo')
		videoContext.play()
		console.log('videoContext', videoContext)
		// songZeroPopup.value.video.play()
	}

	// 文字介绍
	let songContent = ref(['我是第一段文字我是第一段文字我是第一段文字我是第一段文字我是第一段文字我是第一段文字', '我是第二段文字我是第二段文字我是第二段文字我是第二段文字',
		'我是第三段文字我是第三段文字我是第三段文字'
	])
	let songOnePopup = ref()

	function songSelectOne() {
		if (songContent.value.length == 0) {
			uni.showToast({
				title: '暂无文字介绍'
			})
			return
		}
		songOnePopup.value.popup.open('center')
	}

	function songConfirm() {
		songOnePopup.value.popup.close()
	}
</script>



<style lang="less">
	.box-bg {
		display: flex;
		flex-direction: row;
		align-items: flex-start;
		-webkit-flex-wrap: wrap;
		flex-wrap: wrap;
		padding: 30rpx;
		box-sizing: border-box;
	}

	.video_content {
		width: 100%;
		height: 100%;
	}

	.box_item {
		width: 200rpx;
		height: 200rpx;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		border-radius: 16rpx;
		border: 4rpx solid #67c6f8;
		position: relative;


		&:not(:nth-child(3n + 1)) {
			margin-left: 40rpx;
		}

		&:not(:nth-child(-n + 3)) {
			margin-top: 26rpx;
		}

		.bi_tip {
			width: 40rpx;
			height: 40rpx;
			position: absolute;
			top: 6rpx;
			right: 6rpx;
		}

		.bi_icon {
			width: 100rpx;
			height: 100rpx;
		}

		.bi_copy {
			font-weight: 500;
			color: #5fbbf8;
		}
	}
</style>