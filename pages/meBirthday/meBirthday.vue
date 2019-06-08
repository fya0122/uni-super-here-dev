<template>
	<view class="page page-fill">
		<form @submit="formSubmitBirthday">
			<view class="page-block" style="margin-top: 20upx;">
				<picker mode="date" @change="dateChange">
					<view class="birthday">{{ birthday }}</view>
				</picker>
			</view>
			<button type="primary" form-type="submit" class="submitBtn">提交</button>
		</form>
	</view>
</template>

<script>
	import common from '../../common/common.js'
	export default {
		data() {
			return {
				birthday: "",
				globalUser: {}
			};
		},
		onLoad() {
			const globalUser = uni.getStorageSync('cyyg_userInfo')
			this.globalUser = globalUser;
			this.birthday = globalUser.birthday;
		},
		methods: {
			dateChange(e) {
				if (e.detail.value) {
					this.birthday = e.detail.value;
				}
			},
			formSubmitBirthday() {
				const birthday = this.birthday;
				uni.request({
					url: common.api_base_url + "/user/modifyUserinfo?qq=" + common.qq,
					data: {
						"userId": this.globalUser.id,
						"birthday": birthday
					},
					header: {
						"headerUserId": this.globalUser.id,
						"headerUserToken": this.globalUser.userUniqueToken
					},
					method: "POST",
					success: ((res) => {
						const resData = res.data;
						if (resData.status === 200) {
							const userInfo = resData.data;
							uni.setStorageSync("cyyg_userInfo", userInfo);
							uni.navigateBack({
								delta: 1
							})
						} else if (resData.status === 502 || resData.status === 500) {
							uni.showToast({
								title: res.data.msg,
								image: "../../static/icos/error.png",
								duration: 2000
							})
						}
					})
				})
			}
		}
	}
</script>

<style scoped>
	/* 页面铺满屏幕 */
	.page-fill {
		width: 100%;
		height: 100%;
		position: absolute;
	}

	.birth-input {
		background-color: white;
		height: 80upx;
		line-height: 40upx;
		padding-left: 20upx;
	}

	.birthday {
		background-color: white;
		height: 80upx;
		padding-left: 20upx;
		padding-top: 30upx;
	}

	.submitBtn {
		width: 95%;
		margin-top: 40upx;
	}
</style>
