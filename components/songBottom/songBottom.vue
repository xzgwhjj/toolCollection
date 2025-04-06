<template>
	<view class="song_bottom">
		<view v-for="(item,index) in bottomList" :key="index" class="sb_item" @tap="changeSelectIndex(item)">
			<image class="si_icon" :src="item.icon" mode="aspectFit"></image>
			<view v-if="item.value == selectIndex" class="select_circle" :style="{'backgroundColor' : item.color}">
			</view>
		</view>
	</view>
</template>

<script setup>
	import {
		ref
	} from 'vue';

	const props = defineProps({
		selectIndex: {
			type: Number,
			default: 0
		}
	})
	const emits = defineEmits(['changeSelect'])

	let bottomList = ref([{
		icon: '/static/images/icon_history.png',
		text: '历史',
		value: 1,
		color: '#67c6f8',
		url: '/pages/copySong/history'
	}])

	function changeSelectIndex(item) {

		// console.log("index", item.value)
		// if (selectIndex.value == item.value) return
		// selectIndex.value = item.value
		uni.navigateTo({
			url: item.url
		})
	}
</script>

<style lang="less">
	.song_bottom {
		width: 100%;
		box-shadow: 0px 0px 12px rgba(0, 0, 0, .12);
		border-radius: 16rpx;
		display: flex;
		justify-content: space-around;
		align-items: center;
		padding: 25rpx;
		box-sizing: border-box;
		background-color: #ffffff;

		.sb_item {
			flex: 1;
			display: flex;
			flex-direction: column;
			justify-content: center;
			align-items: center;
		}

		.si_icon {
			width: 50rpx;
			height: 50rpx;
		}

		.select_circle {
			width: 10rpx;
			height: 10rpx;
			border-radius: 50%;
			margin-top: 10rpx;
		}
	}
</style>