<template>
	<view class="page page-fill">

		<view class="page-block info-list">
			<!-- 头像 -->

			<view class="item-wapper face-line-upbottom" @click="operator">
				<view class="info-words">头像</view>

				<view class="right-wapper">
					<image :src="globalUser.faceImage" class="face"></image>
					<view class="arrow-block">
						<image src="../../static/icos/left-gray-arrow.png" class="arrow-ico"></image>
					</view>
				</view>
			</view>

			<view class="line-top">
				<view class="line"></view>
			</view>

			<!-- 昵称 -->
			<view class="item-wapper" @click="modifyNickname">
				<view class="info-words">昵称</view>

				<view class="right-wapper">
					<view class="gray-fields">
						{{ globalUser.nickname }}
					</view>
					<view class="arrow-block">
						<image src="../../static/icos/left-gray-arrow.png" class="arrow-ico"></image>
					</view>
				</view>
			</view>

			<view class="line-top">
				<view class="line"></view>
			</view>

			<!-- 生日 -->
			<view class="item-wapper" @click="modifyBirthday">
				<view class="info-words">生日</view>

				<view class="right-wapper">
					<view class="gray-fields">
						{{ globalUser.birthday }}
					</view>
					<view class="arrow-block">
						<image src="../../static/icos/left-gray-arrow.png" class="arrow-ico"></image>
					</view>
				</view>
			</view>

			<view class="line-top">
				<view class="line"></view>
			</view>

			<!-- 性别 -->
			<view class="item-wapper" @click="modifySex">
				<view class="info-words">性别</view>

				<view class="right-wapper">
					<view class="gray-fields">

						<view v-if="globalUser.sex === 1">
							男
						</view>
						<view v-else-if="globalUser.sex === 0">
							女
						</view>
						<view v-else>
							未选择
						</view>

					</view>
					<view class="arrow-block">
						<image src="../../static/icos/left-gray-arrow.png" class="arrow-ico"></image>
					</view>
				</view>
			</view>

		</view>

		<view class="footer-wapper">
			<view class="footer-words" @click="cleanStorage">
				清理缓存
			</view>
			<view class="footer-words" style="margin-top: 10upx;" @click="logout">
				退出登录
			</view>
		</view>

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
		onShow() {
			const globalUser = uni.getStorageSync('cyyg_userInfo')
			if (globalUser) {
				this.globalUser = globalUser
			}
		},
		methods: {
			modifySex() { // 更改性别
				uni.navigateTo({
					url: "../sex/sex"
				})
			},
			modifyBirthday() { // 更改生日
				uni.navigateTo({
					url: "../meBirthday/meBirthday"
				})
			},
			modifyNickname() { // 更改名字
				uni.navigateTo({
					url: "../meNickname/meNickname"
				})
			},
			operator() { // 预览和更改头像
				let globalUser = uni.getStorageSync("cyyg_userInfo");
				if (globalUser) {
					uni.showActionSheet({
						itemList: ["查看我的头像", "从相册选择上传"],
						success(res) {
							let index = res.tapIndex;
							if (index === 0) {
								// 预览头像
								let faceArr = [];
								faceArr.push(globalUser.faceImage);
								uni.previewImage({
									urls: faceArr,
									current: faceArr[0]
								})
							} else if (index === 1) {
								// 选择上传头像
								uni.chooseImage({
									count: 1, // 最多可以选择的图片张数，默认9	
									sizeType: ["compressed"], // original 原图，compressed 压缩图
									sourceType: ["album"], // album 从相册选图，camera 使用相机
									success(res) {
										// 获得临时路径，其实就是图片的路径了
										let tempFilePath = res.tempFilePaths[0];
										// #ifdef H5
										uni.navigateTo({
											url: "../meFace/meFace?tempFilePath=" + tempFilePath
										})
										// #endif
										// #ifndef H5
										uni.navigateTo({
											url: "../faceCrop/faceCrop?tempFilePath=" + tempFilePath
										})
										// #endif
									}
								})

							}
						}
					})
				} else {
					uni.showToast({
						title: '尚未登录',
						icon: 'none'
					})
				}
			},
			cleanStorage() { // 清理缓存
				uni.clearStorageSync();
				uni.showToast({
					title: "清理缓存成功",
					mask: false,
					duration: 1500
				})
				uni.switchTab({
					url: "../me/me"
				})
			},
			logout() { // 退出登录
				const globalUser = uni.getStorageSync('cyyg_userInfo')
				uni.request({
					url: common.api_base_url + '/user/logout?userId=' + globalUser.id + '&qq=' + common.qq,
					method: "POST",
					success: (res) => {
						if (res.data.status === 200 && res.data.msg === 'OK') {
							uni.showToast({
								title: "退出成功",
								mask: false,
								duration: 1500
							})
							uni.removeStorageSync("cyyg_userInfo");
							uni.switchTab({
								url: "../me/me"
							})
						}
					}
				});
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

	.info-list {
		/* margin-top: 20upx; */
		padding: 0upx 30upx;
	}

	.info-words {
		color: #333333;
		font-size: 16px;
		width: 25%;
		line-height: 80upx;
		/* font-weight: bold; */
	}

	.face {
		width: 80upx;
		height: 80upx;
		border-radius: 50%;
	}

	.arrow-block {
		margin-left: 10upx;
		line-height: 86upx;
	}

	.arrow-ico {
		width: 30upx;
		height: 30upx;
	}

	.item-wapper {
		display: flex;
		/* margin-top: 20upx; */
		flex-direction: row;
		justify-content: flex-start;
	}

	.face-line-upbottom {
		margin-top: 20upx;
		/* margin-bottom: 20upx; */
		padding-top: 20upx;
		padding-bottom: 20upx;
	}

	.line-top {
		/* margin-top: 20upx; */
	}

	.right-wapper {
		width: 80%;
		display: flex;
		flex-direction: row;
		justify-content: flex-end;
	}

	.gray-fields {
		font-size: 14px;
		color: darkgray;
		line-height: 80upx;
	}

	/* 底部操作 start */
	.footer-wapper {
		position: fixed;
		bottom: 0;

		display: flex;
		flex-direction: column;
		width: 100%;
	}

	.footer-words {
		text-align: center;
		background-color: white;
		padding: 20upx;

		color: #333333;
		font-size: 16px;
	}

	/* 底部操作 end */
</style>
