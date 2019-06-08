<template>
	<view class="page page-fill">
		<view class="header">
			<view v-if="isLogin">
				<image :src="userInfo.faceImage" class="face"></image>
			</view>
			<view v-else>
				<image src="http://122.152.205.72:88/group1/M00/00/05/CpoxxFw_-5-AFyVyAABLIH8xBTw233.png" class="face"></image>
			</view>
			<view class="info-wapper" v-if="isLogin">
				<view class="nickname">
					{{ userInfo.nickname }}
				</view>
				<view class="nav-info">ID：{{ userInfo.id }}</view>
			</view>
			<view v-else>
				<view @click="gotoPageRegistLogin" class="nickname regist-login">
					注册/登录
				</view>
			</view>
			<view class="set-wapper" v-if="isLogin">
				<image @click="gotoPageMeInfo" src="../../static/icos/settings.png" class="settings"></image>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				isLogin: false,
				userInfo: {}
			}
		},
		onShow() {
			const userInfo = this._checkUserIsLogin()
			if (userInfo) {
				this.isLogin = true
				this.userInfo = userInfo
			} else {
				this.isLogin = false
				this.userInfo = {}
			}
		},
		methods: {
			_checkUserIsLogin() {
				const userInfo = uni.getStorageSync("cyyg_userInfo");
				if (userInfo !== null && userInfo !== "" && userInfo !== undefined) {
					return userInfo
				} else {
					return null
				}
			},
			gotoPageMeInfo() {
				uni.navigateTo({
					url: '../meInfo/meInfo'
				})
			},
			gotoPageRegistLogin() {
				uni.navigateTo({
					url: '../registLogin/registLogin'
				})
			}
		}
	}
</script>

<style scoped>
	.page-fill {
		width: 100%;
		height: 100%;
		background-color: #F7F7F7;
	}

	/* 头部个人信息 start */
	.header {
		/* #ifdef MP */
		padding: 0upx 30upx 40upx 30upx;
		/* #endif */
		/* #ifndef MP */
		padding: 120upx 30upx 40upx 30upx;
		/* #endif */
		background-color: #ffd655;
		background: url(http://122.152.205.72:88/group1/M00/00/03/CpoxxFwu_1OAYFAIAAAzO_Q1tPg693.png) repeat;

		display: flex;
		flex-direction: row;
		justify-content: flex-start;
	}

	.face {
		width: 120upx;
		height: 120upx;
		border-radius: 50%;
		flex-shrink: 0;
	}

	.info-wapper {
		width: 80%;
		margin-left: 40upx;
		display: flex;
		flex-direction: column;
	}

	.nickname {
		color: #6a5018;
		font-size: 18px;
		font-weight: bold;
	}

	.regist-login {
		margin-left: 60upx;
		margin-top: 30upx;
	}


	.nav-info {
		color: #a38e62;
		font-size: 14px;
		margin-top: 10upx;
	}

	.set-wapper {
		display: flex;
		flex-direction: row;
		justify-content: flex-end;
		width: 15%;
	}

	.settings {
		width: 40upx;
		height: 40upx;
	}

	/* 头部个人信息 end */

	/* 中间的 NEXT 大LOGO start */
	.body {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		align-items: center;
	}

	.big-next {
		font-size: 160upx;
		color: #F7F7F7;
	}

	/* 中间的 NEXT 大LOGO end */
</style>
