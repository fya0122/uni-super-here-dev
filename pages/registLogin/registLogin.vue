<template>
	<view class="body">
		<form @submit="formSubmit">
			<view class="face-wapper">
				<image src="../../static/icos/default-face.png" class="face"></image>
			</view>

			<view class="info-wapper">
				<label class="words-lbl">账号</label>
				<input name="username" type="text" value="" class="input" placeholder="请输入用户名" placeholder-class="graywords" />
			</view>

			<view class="info-wapper" style="margin-top: 40upx;">
				<label class="words-lbl">密码</label>
				<input name="password" type="text" value="" password="true" class="input" placeholder="请输入密码" placeholder-class="graywords" />
			</view>

			<button type="primary" form-type="submit" style="margin-top: 60upx;width: 90%;">注册/登录</button>
		</form>


		<!-- 第三方登录H5不支持，所以隐藏掉 -->
		<!-- #ifndef H5 -->
		<view class="third-wapper">

			<view class="third-line">
				<view class="single-line">
					<view class="line"></view>
				</view>

				<view class="third-words">第三方账号登录</view>

				<view class="single-line">
					<view class="line"></view>
				</view>
			</view>

			<view class="third-icos-wapper">
				<!-- 5+app 用qq/微信/微博 登录 小程序用微信小程序登录 h5不支持 -->
				<!-- #ifdef APP-PLUS -->
				<image src="../../static/icos/weixin.png" data-logintype="weixin" @click="appOAuthLogin" class="third-ico"></image>
				<image src="../../static/icos/QQ.png" data-logintype="qq" @click="appOAuthLogin" class="third-ico" style="margin-left: 80upx;"></image>
				<image src="../../static/icos/weibo.png" data-logintype="sinaweibo" @click="appOAuthLogin" class="third-ico" style="margin-left: 80upx;"></image>
				<!-- #endif -->
				<!-- #ifdef MP-WEIXIN -->
				<button open-type='getUserInfo' @getuserinfo="wxLogin" class="third-btn-ico">
				</button>
				<!-- #endif -->
			</view>
		</view>
		<!-- #endif -->
	</view>
</template>

<script>
	import common from '../../common/common.js'
	export default {
		data() {
			return {

			};
		},
		methods: {
			// 第三方授权登录
			appOAuthLogin(e) {
				// 获取用户的登录类型
				let logintype = e.currentTarget.dataset.logintype;

				// 授权登录
				uni.login({
					provider: logintype,
					success(loginRes) {

						// 授权登录成功以后，获取用户的信息
						uni.getUserInfo({
							provider: logintype,
							success(info) {
								let userInfo = info.userInfo;
								let face = "";
								let nickname = "";
								let openIdOrUid = "";
								if (logintype == "weixin") {
									face = userInfo.avatarUrl;
									nickname = userInfo.nickName;
									openIdOrUid = userInfo.openId;
								} else if (logintype == "qq") {
									openIdOrUid = userInfo.openId;
									nickname = userInfo.nickname;
									face = userInfo.figureurl_qq_2;
								} else if (logintype == "sinaweibo") {
									openIdOrUid = userInfo.id;
									nickname = userInfo.nickname;
									face = userInfo.avatar_large;
								}

								// 调用开发者后台，执行一键注册或登录
								uni.request({
									url: common.api_base_url + "/appUnionLogin/" + logintype + '?qq=' + common.qq,
									data: {
										"openIdOrUid": openIdOrUid,
										"nickname": nickname,
										"face": face
									},
									method: "POST",
									success(result) {
										if (result.data.status === 200) {
											let new_userInfo = result.data.data;
											// 保存用户信息到全局的缓存中
											uni.setStorageSync("cyyg_userInfo", new_userInfo);
											// 切换页面跳转，使用tab切换的api
											uni.switchTab({
												url: "../me/me"
											});
										}
									}
								})
							}
						})
					}
				});
			},
			// 实现在微信小程序端的微信登录
			wxLogin(e) {
				// 通过微信开发能力，获得微信用户的基本信息
				let userInfo = e.detail.userInfo;

				// 实现微信登录
				uni.login({
					provider: "weixin",
					success(loginResult) {
						// 获得微信登录的code：授权码
						let code = loginResult.code;
						// 设置登录到哪个对于的微信小程序，大家可以根据自己的后端去实现业务参数
						// [0:NEXT超英预告][1:超英预告][2:NEXT学院电影预告]
						let loginToWhichMP = 1;
						uni.request({
							url: common.api_base_url + "/mpWXLogin/" + code + '?qq=' + common.qq,
							data: {
								"avatarUrl": userInfo.avatarUrl,
								"nickName": userInfo.nickName,
								"whichMP": loginToWhichMP
							},
							method: "POST",
							success(userResult) {
								let new_userInfo = userResult.data.data;
								uni.setStorageSync("cyyg_userInfo", new_userInfo);
								uni.switchTab({
									url: "../me/me"
								});
							}
						});
					}
				})
			},
			// 账号密码登录
			formSubmit(e) {
				let username = e.detail.value.username;
				let password = e.detail.value.password;
				uni.request({
					url: common.api_base_url + '/user/registOrLogin?qq=' + common.qq,
					data: {
						"username": username,
						"password": password
					},
					method: "POST",
					success: (res) => {
						if (res.data.status === 200) {
							var userInfo = res.data.data;
							uni.setStorageSync("cyyg_userInfo", userInfo);
							uni.switchTab({
								url: "../me/me"
							});
						} else if (res.data.status === 500) {
							uni.showToast({
								title: res.data.msg,
								duration: 2000,
								image: "../../static/icos/error.png"
							})
						}
					}
				});
			}
		}
	}
</script>

<style scoped>
	.body {
		border-top: solid 1px #DBDBDA;
		padding: 0 40upx;
	}

	/* 头像 start */
	.face-wapper {
		display: flex;
		flex-direction: row;
		justify-content: center;

		margin-top: 120upx;
		margin-bottom: 120upx;
	}

	.face {
		width: 160upx;
		height: 160upx;
	}

	/* 头像 end */

	/* 注册登录 start */
	.info-wapper {
		display: flex;
		flex-direction: row;
		justify-content: center;

		border-bottom: solid 1px #DBDBDA;

		padding-bottom: 20upx;
	}

	.words-lbl {
		color: #808080;
	}

	.input {
		width: 500upx;
		margin-left: 40upx;
	}

	.graywords {
		color: #EAEAEA;
	}

	/* 注册登录 end */

	/* 第三方登录 start */
	.third-wapper {
		width: 100%;
		/* 固定底部 */
		/* bottom: 0;
	position: fixed; */

		margin-top: 60upx;

	}

	.third-line {
		display: flex;
		flex-direction: row;
		justify-content: center
	}

	.third-words {
		color: #A9A9A9;
		font-size: 13px;

		display: flex;
		flex-direction: column;
		justify-content: center;
	}

	.single-line {
		padding: 15upx 20upx;
		width: 25%;
		align-items: center;
	}

	.third-icos-wapper {
		margin-top: 30upx;

		display: flex;
		flex-direction: row;
		justify-content: center
	}

	.third-ico {
		width: 60upx;
		height: 60upx;
	}

	.third-btn-ico {
		background-image: url(http://122.152.205.72:88/group1/M00/00/05/CpoxxFxFO-yAOjTaAAATCIZEzRU503.png);
		width: 60upx;
		height: 60upx;
		background-color: white;
		background-size: 60upx 60upx;
		background-repeat: no-repeat;
		border: none;
		padding: 0;
	}

	button::after {
		border: none;
	}

	/* 第三方登录 end */
</style>
