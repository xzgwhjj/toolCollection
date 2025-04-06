<template>
	<view class="search" :style="{'borderColor' : isFocus ? color : ''}">
		<image src="/static/images/icon_search.png" mode="aspectFit" class="search_icon"></image>
		<uni-easyinput v-model="value" :styles="{'color' : isFocus ? color : ''}" :inputBorder="false"
			:primaryColor="color" placeholderStyle="color:#aaa;" :placeholder="placeholder" @confirm="onConfirm"
			@focus="onFocus" @blur="onBlur"></uni-easyinput>
	</view>
</template>

<script setup>
	import {
		ref
	} from 'vue';

	const props = defineProps({
		color: {
			type: String,
			default: '#67c6f8'
		},
		placeholder: {
			type: String,
			default: '请输入歌词'
		}
	})
	const emits = defineEmits(['changeSelect', 'searchResult'])
	let value = defineModel('value')

	let isFocus = ref(false)

	function onFocus() {
		isFocus.value = true
	}

	function onBlur() {
		isFocus.value = false
		emits('searchResult')
	}

	function onConfirm() {
		emits('searchResult')
	}
</script>

<style lang="less">
	.search {
		width: 100%;
		height: 100%;
		display: flex;
		align-items: center;
		box-shadow: 0px 0px 12rpx rgba(0, 0, 0, .12);
		border-radius: 50rpx;
		padding: 10rpx 20rpx;
		box-sizing: border-box;
		border: 2rpx solid transparent;

		.search_icon {
			width: 40rpx;
			height: 40rpx;
			margin-right: 10rpx;
		}
	}
</style>