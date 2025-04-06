<template>
	<uni-popup ref="popup" :type="type" background-color="#fff"
		:border-radius="type == 'bottom' ? '10px 10px 0 0' : '10px 10px 10px 10px'" :is-mask-click="isMaskClick">
		<view class="popup-content" v-if="type == 'bottom'">
			<view class="pc_top" :style="{backgroundColor: color + '30'}"></view>
			<view class="pc_content">
				<view v-for="item in selectList" v-if="selectList.length > 0" :key="item.value" class="pc_item"
					@tap="gotoSelect(item.value)">
					{{item.label}}
				</view>
			</view>
		</view>
		<template v-if="type == 'center'">
			<view v-if="useType == 'dialog'" class="dialog">
				<view v-if="dialogContent.length > 0" class="dialog_content">
					<view v-for="item,index in dialogContent" :key="index" class="dc_item">{{item}}</view>
				</view>
				<view class="dialog_button">
					<view class="db_cancel" @tap="cancel" v-if="isCancel">{{dialogCancel}}</view>
					<view class="db_confirm" :style="{color:color}" @tap="confirm">{{dialogConfirm}}</view>
				</view>
			</view>
			<view v-if="useType == 'video'" class="video" :style="{borderColor: color}">
				<slot name="video"></slot>
			</view>
		</template>
		<template v-if="type == 'dialog'">
			<uni-popup-dialog ref="inputClose" mode="input" :title="inputSetting.title" :value="inputSetting.value"
				:placeholder="inputSetting.placeholder" @confirm="dialogInputConfirm"></uni-popup-dialog>
		</template>
	</uni-popup>
</template>

<script setup>
	import {
		ref
	} from 'vue';

	const props = defineProps({
		type: {
			type: String,
			default: 'bottom'
		},
		selectList: {
			type: Array,
			default: []
		},
		color: {
			type: String,
			default: '#67c6f8'
		},
		useType: {
			type: String,
			default: 'dialog'
		},
		dialogContent: {
			type: Array,
			default: []
		},
		dialogCancel: {
			type: String,
			default: '取消'
		},
		dialogConfirm: {
			type: String,
			default: '确定'
		},
		isCancel: {
			type: Boolean,
			default: false
		},
		isMaskClick: {
			type: Boolean,
			default: true
		},
		videoSetting: {
			type: Object,
			default: {
				src: '',
				autoplay: false
			}
		},
		inputSetting: {
			type: Object,
			default: {
				title: '歌名修改',
				value: '',
				placeholder: '请输入歌名'
			}
		}
	})
	const emits = defineEmits(['gotoSelect', 'cancel', 'confirm', 'dialogInputConfirm'])

	let popup = ref()
	defineExpose({
		popup
	})

	function gotoSelect(index) {
		emits('gotoSelect', index)
	}

	function cancel() {
		emits('cancel')
	}

	function confirm() {
		emits('confirm')
	}

	function dialogInputConfirm(val) {
		emits('dialogInputConfirm', val)
	}
</script>

<style lang="less">
	.popup-content {
		width: 750rpx;
		padding: 20rpx;
		box-sizing: border-box;
		display: flex;
		flex-direction: column;
		align-items: center;

		.pc_top {
			width: 150rpx;
			height: 10rpx;
			border-radius: 10rpx;
		}

		.pc_content {
			width: 100%;
			display: flex;
			flex-direction: column;
			align-items: center;
			margin-top: 20rpx;

			.pc_item {
				width: 100%;
				border-bottom: 2rpx solid #efefef;
				padding: 40rpx 0;
				box-sizing: border-box;
				text-align: center;
				font-size: 36rpx;
			}
		}
	}

	.dialog {
		width: 550rpx;

		.dialog_content {
			width: 100%;
			padding: 30rpx;
			box-sizing: border-box;
			color: #333;

			.dc_item {
				text-indent: 2em;
				margin-top: 10rpx;
			}
		}

		.dialog_button {
			display: flex;
			justify-content: space-around;
			margin-top: 10rpx;

			.db_cancel,
			.db_confirm {
				flex: 1;
				display: flex;
				justify-content: center;
				align-items: center;
				height: 90rpx;
				border-top: 2rpx solid #eee;
			}

			.db_cancel {
				border-right: 2rpx solid #eee;
			}
		}
	}

	.video {
		width: 650rpx;
		height: calc(650rpx * 1.78);
		border: 8rpx solid #eee;
		padding: 20rpx;
		box-sizing: border-box;
		border-radius: 10px;
	}
</style>