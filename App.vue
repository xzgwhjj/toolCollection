<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		globalData: {
			// 日期转换
			dateTime() {
				const dateTime = new Date();
				// 获取年月日
				const year = dateTime.getFullYear(); // 获取完整的年份
				let month = dateTime.getMonth() + 1; // getMonth() 返回的月份是从 0 开始的，所以要加 1
				let day = dateTime.getDate();
				if (month < 10) {
					month = `0${month}`
				}
				if (day < 10) {
					day = `0${day}`
				}
				let hours = dateTime.getHours().toString().padStart(2, '0');
				let minutes = dateTime.getMinutes().toString().padStart(2, '0');
				let seconds = dateTime.getSeconds().toString().padStart(2, '0');
				return year + month + day + hours + minutes + seconds;
			},
			isOneEnter(fn) {
				const isOne = uni.getStorageSync('isOne')
				if (!isOne) {
					uni.showModal({
						title: '温馨提醒',
						content: '本程序采用的是本地存储，一旦删除小程序，历史记录将会清空，请不要将重要信息储存在本程序中',
						confirmText: '知道了',
						showCancel: false,
						success: (res) => {
							if (res.confirm) {
								uni.setStorageSync('isOne', 1)
								fn()
							}
						}
					})
				} else {
					fn()
				}
			}
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	/*每个页面公共css */
</style>