<template>
	<view class="page">

		<!-- 视频播放 start -->
		<view class="player">
			<video id="myTrailer" :src="trailerInfo.trailer" :poster="trailerInfo.poster" class="movie" controls></video>
		</view>
		<!-- 视频播放 end -->

		<!-- 影片基本信息 start -->
		<view class="movie-info">
			<image :data-cover="trailerInfo.cover" @click="gotoCover" :src="trailerInfo.cover" class="cover"></image>

			<view class="movie-desc">
				<view class="title">{{ trailerInfo.name }}</view>
				<view class="basic-info">{{ trailerInfo.basicInfo }}</view>
				<view class="basic-info">{{ trailerInfo.originalName }}</view>
				<view class="basic-info">{{ trailerInfo.totalTime }}</view>
				<view class="basic-info">{{ trailerInfo.releaseDate }}</view>
				<view class="score-block">
					<view class="big-score">
						<view class="score-words">综合评分：</view>
						<view class="movie-score">{{ trailerInfo.score }}</view>
					</view>
					<view class="score-stars">
						<uni-rate v-if="trailerInfo.score > 0" size="14" :value="trailerInfo.score / 2" max="5"></uni-rate>
						<view class="prise-counts">
							{{ trailerInfo.prisedCounts }} 人点赞
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 影片基本信息 end -->


		<view class="line-wapper">
			<view class="line"></view>
		</view>

		<!-- 剧情介绍 start -->
		<view class="plots-block">
			<view class="plots-title">剧情介绍</view>
			<view class="plots-desc">{{ trailerInfo.plotDesc }}</view>
		</view>
		<!-- 剧情介绍 end -->

		<!-- 演职人员 start -->
		<view class="scroll-block">
			<view class="plots-title">演职人员</view>
			<scroll-view scroll-x class="scroll-list">
				<!-- 平时请禁止用index做key -->
				<view :key="director.staffId" class="actor-wapper" v-for="(director, staffIndex) of directorArray">
					<image :src="director.photo" class="single-actor" mode="aspectFill" @click="lookStaffs" :data-staffIndex="staffIndex"></image>
					<view class="actor-name">{{ director.name }}</view>
					<view class="actor-role">{{ director.actName }}</view>
				</view>
				<!-- 平时请禁止用index做key -->
				<view :key="actor.staffId" class="actor-wapper" v-for="(actor, actorIndex) of actorArray">
					<image :src="actor.photo" class="single-actor" mode="aspectFill" @click="lookStaffs" :data-staffIndex="actorIndex + directorArray.length"></image>
					<view class="actor-name">{{ actor.name }}</view>
					<view class="actor-role">{{ actor.actName }}</view>
				</view>
			</scroll-view>
		</view>
		<!-- 演职人员 end -->

		<!-- 剧照 start -->
		<view class="scroll-block">
			<view class="plots-title">剧照</view>
			<scroll-view scroll-x class="scroll-list">
				<image :key="item" v-for="(item, index) of plotPicsArray" :src="item" class="plot-image" mode="aspectFill" @click="lookClickMe"
				 :data-index="index"></image>
			</scroll-view>
		</view>
		<!-- 剧照 end -->

	</view>
</template>

<script>
	import common from '../../common/common.js'
	import {
		uniRate
	} from '@dcloudio/uni-ui'
	export default {
		data() {
			return {
				movieId: '',
				trailerInfo: {}, // 要渲染到页面上面的
				plotPicsArray: [], // 剧照
				directorArray: [], // 导演列表
				actorArray: [] // 演员列表
			}
		},
		components: {
			uniRate
		},
		// 页面初次渲染完成，获得视频上下文对象
		onReady() {
			this.videoContext = uni.createVideoContext('myTrailer');
		},
		onHide() {
			// 页面被隐藏的时候，暂停播放
			this.videoContext.pause();
		},
		onLoad(e) {
			// 通过api修改导航栏的属性
			uni.setNavigationBarColor({
				frontColor: "#ffffff",
				backgroundColor: "#000000"
			});
			this.movieId = e.id
			this._getCurrentMoviePageData(this.movieId)
		},
		// 此函数仅仅只支持在小程序端的分享，分享到微信群或者微信好友
		onShareAppMessage(res) {
			return {
				title: this.trailerInfo.name,
				path: '/pages/movie/movie?id=' + this.trailerInfo.id
			}
		},
		// 监听导航栏的按钮
		onNavigationBarButtonTap(e) {
			let index = e.index;
			let trailerInfo = this.trailerInfo;
			let trailerId = trailerInfo.id;
			let trailerName = trailerInfo.name;
			let cover = trailerInfo.cover;
			let poster = trailerInfo.poster;
			// index 为0 则分享
			if (index === 0) {
				uni.share({
					provider: "weixin",
					scene: "WXSenceTimeline",
					type: 0,
					href: "http://www.imovietrailer.com/#/pages/movie/movie?id=" + trailerId,
					title: "NEXT超英预告：《" + trailerName + "》",
					summary: "NEXT超英预告：《" + trailerName + "》",
					imageUrl: cover,
					success: function(res) {
						console.log("success:" + JSON.stringify(res));
					}
				});
			}
		},
		methods: {
			// 获取基本信息
			_getCurrentMoviePageData(id) {
				uni.showLoading({
					title: '加载中',
					mask: true
				});
				uni.request({
					url: common.api_base_url + '/search/trailer/' + id,
					header: {
						'Content-Type': 'application/x-www-form-urlencoded'
					},
					data: {
						qq: common.qq
					},
					method: 'POST',
					success: ((res) => {
						if (res.data.status === 200 && res.data.msg === 'OK') {
							this.trailerInfo = res.data.data
							this.plotPicsArray = JSON.parse(res.data.data.plotPics)
						} else {
							this.trailerInfo = {}
							this.plotPicsArray = []
						}
					}),
					fail: ((err) => {
						console.log(err)
						this.trailerInfo = {}
						this.plotPicsArray = []
					}),
					complete: (() => {
						this._directorArrayList(id)
					})
				})
			},
			// 获取导演
			_directorArrayList(id) {
				uni.request({
					url: common.api_base_url + '/search/staff/' + id + '/1',
					method: 'POST',
					header: {
						'Content-Type': 'application/x-www-form-urlencoded'
					},
					data: {
						qq: common.qq
					},
					success: ((res) => {
						if (res.data.status === 200 && res.data.msg === 'OK') {
							this.directorArray = res.data.data
						} else {
							this.directorArray = []
						}
					}),
					fail: ((err) => {
						console.log(err)
						this.directorArray = []
					}),
					complete: (() => {
						// 得到演员
						this._actorArrayList(id)
					})
				})
			},
			// 获取演员
			_actorArrayList(id) {
				uni.request({
					url: common.api_base_url + '/search/staff/' + id + '/2',
					method: 'POST',
					header: {
						'Content-Type': 'application/x-www-form-urlencoded'
					},
					data: {
						qq: common.qq
					},
					success: ((res) => {
						if (res.data.status === 200 && res.data.msg === 'OK') {
							this.actorArray = res.data.data


							console.log('v2')
							console.log(this.trailerInfo)
							console.log(this.plotPicsArray)
							console.log(this.directorArray)
							console.log(this.actorArray)
						} else {
							this.actorArray = []
						}
					}),
					fail: ((err) => {
						console.log(err)
						this.actorArray = []
					}),
					complete: (() => {
						uni.hideLoading()
					})
				})
			},
			// 点击剧照的任何一张图片的时候
			lookClickMe(e) {
				const index = e.currentTarget.dataset.index;
				uni.previewImage({
					current: this.plotPicsArray[index],
					urls: this.plotPicsArray
				})
			},
			// 点击导演或者演员的时候
			lookStaffs(e) {
				const staffIndex = e.currentTarget.dataset.staffindex;
				// 拼接导演和演员的数组，成为一个新数组
				const directorArray = this.directorArray;
				const actorArray = this.actorArray;
				let newStaffArray = [];
				newStaffArray = newStaffArray.concat(directorArray).concat(actorArray);

				let urls = [];
				for (let i = 0; i < newStaffArray.length; i++) {
					let tempPhoto = newStaffArray[i].photo;
					urls.push(tempPhoto);
				}

				uni.previewImage({
					current: urls[staffIndex],
					urls: urls
				})
			},
			// 点击cover的时候
			gotoCover(e) {
				let cover = e.currentTarget.dataset.cover
				if (cover) {
					uni.navigateTo({
						url: '../cover/cover?cover=' + cover
					})
				} else {
					uni.showToast({
						title: "无cover",
						duration: 2000,
						icon: 'none'
					})
				}
			}
		}
	}
</script>

<style scoped>
	.player {
		display: flex;
		justify-content: center;
		background-color: black;
	}

	.movie {
		width: 100%;
		height: 440upx;
	}

	/* 影片基本信息 start */
	.movie-info {
		padding: 40upx 20upx;
		display: flex;
		flex-direction: row;
		background-color: #f7f4f9;
	}

	.cover {
		width: 280upx;
		height: 380upx;
	}

	.movie-desc {
		display: flex;
		flex-direction: column;
		margin-left: 30upx;
		width: 400upx;
	}

	.title {
		font-size: 30px;
	}

	.basic-info {
		color: darkgray;
		font-size: 13px;
		line-height: 36upx;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
	}

	/* 影片基本信息 end */

	/* 评分块 start */
	.score-block {
		background-color: white;
		margin-top: 20upx;
		width: 360upx;
		height: 90upx;
		padding: 20upx;
		display: flex;
		flex-direction: row;
		justify-content: flex-start;
		/* 追加阴影效果 */
		box-shadow: 3px 2px 10px #dedede;
	}

	.score-words {
		font-size: 12px;
		color: grey;
	}

	.movie-score {
		font-size: 26px;
		font-weight: bold;
		margin-left: 8upx;
		color: #FEAB2A;
		line-height: 60upx;
	}

	.prise-counts {
		font-size: 12px;
		color: grey;
		line-height: 44upx;
	}

	/* 评分块 end */

	/* 剧情介绍 start */
	.plots-block {
		background-color: #f7f4f9;
		padding: 20upx 40upx;
	}

	.plots-title {
		color: #A9A9A9;
		font-size: 14px;
	}

	.plots-desc {
		margin-top: 10upx;
		font-size: 16px;
	}

	/* 剧情介绍 end */

	/* 剧照 start */
	.scroll-block {
		background-color: #f7f4f9;
		padding: 20upx 40upx;
		/* display: flex;
	flex-direction: column; */
	}

	.scroll-list {
		width: 100%;
		white-space: nowrap;
		margin-top: 20upx;
	}

	.plot-image {
		width: 220upx;
		height: 320upx;
		margin-right: 10upx;
	}

	/* 剧照 end */

	/* 演职人员 end */
	.single-actor {
		width: 170upx;
		height: 240upx;
		margin-right: 10upx;
	}

	.actor-wapper {
		display: inline-block;
	}

	.actor-name {
		width: 170upx;
		text-align: center;
		font-size: 15px;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
	}

	.actor-role {
		width: 170upx;
		text-align: center;
		font-size: 13px;
		color: #A9A9A9;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
	}

	/* 演职人员 end */
</style>
