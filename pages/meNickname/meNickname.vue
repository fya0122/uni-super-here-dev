<template>
	<view class="page page-fill">
		<form @submit="formSubmitNickname">
			<view class="page-block" style="margin-top: 20upx;">
				<input type="text" name="nickname" :value="globalUser.nickname" class="input" placeholder="请输入昵称" placeholder-class="graywords"
				 maxlength="10" />
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
				globalUser: {}
			};
		},
		onLoad() {
			let globalUser = uni.getStorageSync('cyyg_userInfo')
			this.globalUser = globalUser;
		},
		methods: {
			formSubmitNickname(e) {
				let nickname = e.detail.value.nickname;
				if (nickname) {
					uni.request({
						url: common.api_base_url + "/user/modifyUserinfo?qq=" + common.qq,
						data: {
							"userId": this.globalUser.id,
							"nickname": nickname
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
	}
</script>

<style scoped>
	/* 页面铺满屏幕 */
	.page-fill {
		width: 100%;
		height: 100%;
		position: absolute;
	}

	.graywords {
		color: #EAEAEA;
	}

	.input {
		height: 80upx;
		line-height: 80upx;
		width: 500upx;
		margin-left: 40upx;
	}

	.submitBtn {
		width: 95%;
		margin-top: 40upx;
	}
</style>
