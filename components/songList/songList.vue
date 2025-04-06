<template>
	<view class="song_list">
		<template v-if="songType == 1">
			<view v-for="(line, index) in textLines" :key="index" @tap="copySong(line,index)"
				:class="currentIndex == (index + 1) ? 'copy_text copy_text_active' : 'copy_text'">
				<view class="ct_left">
					<view :class="currentIndex == (index + 1) ? 'ct_num ct_num_active' : 'ct_num'">{{index + 1}}</view>
					<view class="ct_song">{{ line }}</view>
				</view>
				<image v-if="currentIndex == (index + 1)"
					:class="currentIndex == (index + 1) ? 'ct_icon ct_icon_active' : 'ct_icon'"
					src="/static/images/icon_shuqian.png" mode="aspectFit"></image>
			</view>
		</template>
		<template v-else-if="songType == 2">
			<template v-if="historyList.length > 0">
				<view v-for="(item,index) in historyList" :key="index" @tap="detailCopySong(item)"
					class="copy_text copy_text_history" @longpress="delHistory(item)">
					<view class="ct_left">
						<view :class="(index + 1) % 2 == 1 ? 'ct_num ct_num_active' : 'ct_num'">{{index + 1}}</view>
						<view class="ct_song">
							<text class="cs_name" @tap.stop="updateName(item.name)">{{item.name || '-'}}</text>
							<text class="cs_content">{{item.textList && item.textList[0] || '-'}}</text>
						</view>
					</view>
					<image class="ct_icon" src="/static/images/icon_you.png" mode="aspectFit"></image>
					<view class="ct_index"><text
							class="ci_current">{{item.currentIndex || '0'}}</text>/{{item.total || '0'}}</view>
				</view>
			</template>
			<template v-else>
				<view class="no_history">暂无历史记录</view>
			</template>
			<view></view>
		</template>
	</view>
</template>

<script setup>
	import {
		ref
	} from 'vue';

	const props = defineProps({
		textLines: Array,
		songType: {
			type: Number,
			default: 1
		},
		currentIndex: {
			type: Number,
			default: 0
		},
		historyList: {
			type: Array,
			default: []
		}
	})

	// [
	// 	{
	// 		name:'xxx',
	// 		textList:[],
	// 		currentIndex:1,
	// 		total:10
	// 	}
	// ]

	const emits = defineEmits(['detailCopySong', 'delHistory', 'copySong', 'updateName'])

	function copySong(item, index) {
		emits('copySong', index)
		console.log("currentIndex.value", props.currentIndex)
		uni.setClipboardData({
			data: String(item),
			success: (res) => {
				console.log("res", res)
			}
		})
	}

	// 跳转歌词复制页面
	function detailCopySong(item) {
		emits('detailCopySong', item)
	}

	// 长按删除
	function delHistory(item) {
		console.log("item", item)
		uni.showModal({
			title: '提示',
			content: '确定删除吗?',
			success: function(res) {
				if (res.confirm) {
					emits('delHistory', item)
				} else if (res.cancel) {
					uni.showToast({
						title: '取消删除',
						icon: 'none'
					});
					console.log('用户点击取消');
				}
			}
		});
	}

	// 修改歌名
	function updateName(name) {
		emits('updateName', name)
	}
</script>

<style lang="less">
	.song_list {
		width: 100%;
		padding: 0 30rpx;
		box-sizing: border-box;
	}

	.copy_text {
		border: 4rpx solid transparent;
		box-shadow: 0px 0px 10rpx rgba(0, 0, 0, .12);
		padding: 20rpx;
		display: flex;
		justify-content: space-between;
		align-items: center;
		border-radius: 16rpx;
		transition: border 0.5s;
		position: relative;
		margin-top: 10rpx;

		.ct_left {
			display: flex;
			align-items: center;

			.ct_num {
				width: 50rpx;
				height: 50rpx;
				border-radius: 50%;
				text-align: center;
				line-height: 50rpx;
				border: 4rpx solid #bdd2ef;
				margin-right: 15rpx;
				font-weight: bold;
				transition: all 0.5s;
			}

			.ct_num_active {
				border: 4rpx solid #67c6f8;
				background-color: #67c6f8;
				color: #fff;
			}

			.ct_song {
				display: flex;
				flex-direction: column;

				.cs_name {
					font-size: 36rpx;
					font-weight: bolder;
				}

				.cs_content {
					font-size: 24rpx;
					color: #aaaaaa;
					font-weight: 500;
					margin-top: 8rpx;
				}
			}
		}

		.ct_icon {
			width: 40rpx;
			height: 40rpx;
			/* 应用动画 */
		}

		.ct_index {
			position: absolute;
			top: 4rpx;
			right: 20rpx;
			color: #777777;
			font-size: 20rpx;
			font-weight: 500;

			.ci_current {
				color: #67c6f8;
			}
		}

		.ct_icon_active {
			animation: fadeIn 0.5s ease-in-out;
		}

		@keyframes fadeIn {
			from {
				opacity: 0;
			}

			to {
				opacity: 1;
			}
		}
	}

	.copy_text_history {
		margin-top: 20rpx;
		box-shadow: 0px 0px 12rpx rgba(0, 0, 0, .12);
	}

	.no_history {
		display: flex;
		justify-content: center;
		align-items: center;
		height: 50vh;
		color: #aaaaaa;
		font-size: 24rpx;
	}

	.copy_text_active {
		border: 4rpx solid #67c6f8;
	}
</style>