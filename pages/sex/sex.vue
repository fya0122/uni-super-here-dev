<template>
	<view class="page page-fill">
		<form @submit="formSubmitSex">
			<view class="page-block" style="margin-top: 20upx;">
				<radio-group class="radio-sex" @change="sexChange">
					<label class="radio-single">
						<radio value="1" :checked="sex == 1" />男
					</label>
					<label class="radio-single">
						<radio value="0" :checked="sex == 0" />女
					</label>
				</radio-group>
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
				globalUser: {},
				sex: "-1"
			};
		},
		onLoad() {
			const globalUser = uni.getStorageSync('cyyg_userInfo')
			this.globalUser = globalUser;
			this.sex = globalUser.sex;
		},
		methods: {
			sexChange(e) {
				if (e.detail.value) {
					this.sex = e.detail.value;
				}
			},
			formSubmitSex() {
				const sex = this.sex;
				uni.request({
					url: common.api_base_url + "/user/modifyUserinfo?qq=" + common.qq,
					data: {
						"userId": this.globalUser.id,
						"sex": sex
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

	.radio-sex {
		display: flex;
		flex-direction: column;
	}

	.radio-single {
		padding: 20upx 20upx;
	}

	.submitBtn {
		width: 95%;
		margin-top: 40upx;
	}
</style>
