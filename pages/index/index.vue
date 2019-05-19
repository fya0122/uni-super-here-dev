<template>
	<view class="index-page">
		<!-- 首页轮播 -->
		<swiper :circular="true" class="carousel" :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000">
			<block v-for="item in swiperList" :key="item.id">
				<swiper-item>
					<image :src="item.image"></image>
				</swiper-item>
			</block>
		</swiper>

		<!-- 热门超英 -->
		<view class="page-block super-hot">
			<view class="hot-title-waapper">
				<image class="hot-ico" src="../../static/icos/hot.png" />
				<view class="hot-title">
					热门超英
				</view>
			</view>
		</view>
		<!-- 可滑动的横向 -->
		<scroll-view scroll-x="true" class="page-block hot">
			<view v-for="item in hotSuperheroList" :key="item.id" class="single-poster">
				<view class="poster-wrapper">
					<image class="poster" :src="item.cover"></image>
					<view class="movie-name">
						{{ item.name }}
					</view>
					<view class="movie-score-wrapper">
						<uni-rate size="14" :value="item.score / 2" max="5"></uni-rate>
						<view class="movie-score">
							{{ item.score }}
						</view>
					</view>
				</view>
			</view>
		</scroll-view>
		<!-- 热门超英 -->
		<view class="page-block super-hot">
			<view class="hot-title-waapper">
				<image class="hot-ico" src="../../static/icos/interest.png" />
				<view class="hot-title">
					热门预告
				</view>
			</view>
		</view>
		<!-- 热门预告的那些v-for -->
		<view class="page-block hot-movies">
			<video class="hot-movie-single" controls :poster="item.poster" :key="item.id" v-for="item in trailerSuperheroList"
			 :src="item.trailer"></video>
		</view>
		<!-- 猜你喜欢 -->
		<view class="page-block super-hot">
			<view class="hot-title-waapper">
				<image class="hot-ico" src="../../static/icos/guess-u-like.png" />
				<view class="hot-title">
					猜你喜欢
				</view>
			</view>
		</view>
		<view class="page-block guess-u-like">
			<view :key="item.id" v-for="(item, index) in praiseList" class="single-like-movie">
				<image :src="item.cover" class="like-movie-img"></image>
				<view class="movie-desc">
					<view class="movie-title">
						{{ item.name }}
					</view>
					<uni-rate size="14" :value="item.score / 2" max="5"></uni-rate>
					<view class="movie-info">
						{{ item.basicInfo }}
					</view>
					<view class="movie-info">
						{{ item.releaseDate }}
					</view>
				</view>
				<view class="movie-oper" :data-index="index" @click="praiseMe">
					<image class="praise-ico" src="../../static/icos/praise.png"></image>
					<view class="praise-me">
						点赞
					</view>
					<view :animation="animationDataArr[index]" class="praise-me animation-opacity">
						+1
					</view>
				</view>
			</view>
		</view>
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
				swiperList: [],
				hotSuperheroList: [],
				trailerSuperheroList: [],
				animationData: {},
				praiseList: [],
				animationDataArr: [{}, {}, {}, {}, {}]
			}
		},
		onLoad() {
			// #ifdef APP-PLUS || MP-WEIXIN
			this._setAnimation()
			// #endif
			this._getSwiperData()
		},
		onUnload() {
			// 页面卸载的时候清除动画数据
			// this.animationData = {}
			this.animationDataArr = [{}, {}, {}, {}, {}]
		},
		onPullDownRefresh() {
			this._getSwiperData()
		},
		methods: {
			// 点击操作
			praiseMe(e) {
				// #ifdef APP-PLUS || MP-WEIXIN
				const index = e.currentTarget.dataset.index

				// 构建动画数据，并且通过step来表示这组动画的完成
				this.animation.translateY(-70).opacity(1).step({
					duration: 400
				})
				this.animationData = this.animation
				this.animationDataArr[index] = this.animationData.export()

				// 执行完毕动画以后肯定是要复原的
				setTimeout(() => {
					this.animation.translateY(0).opacity(0).step({
						duration: 0
					})
					this.animationData = this.animation
					this.animationDataArr[index] = this.animationData.export()
				}, 500)
				// #endif
			},
			// 设置动画
			_setAnimation() {
				this.animation = uni.createAnimation()
			},
			// 得到数据
			_getSwiperData() {
				uni.showLoading({
					title: '加载中',
					mask: true
				});
				uni.request({
					url: common.api_base_url + '/index/carousel/list',
					header: {
						'Content-Type': 'application/x-www-form-urlencoded'
					},
					data: {
						qq: common.qq
					},
					method: 'POST',
					success: ((res) => {
						if (res.data.status === 200 && res.data.msg === 'OK') {
							this.swiperList = res.data.data
						} else {
							this.swiperList = []
						}
					}),
					fail: ((err) => {
						console.log(err)
						this.swiperList = []
					}),
					complete: (() => {
						this._getSuperheroData()
					})
				})
			},
			_getSuperheroData() {
				wx.request({
					url: common.api_base_url + '/index/movie/hot?type=superhero',
					method: 'POST',
					data: {
						qq: common.qq
					},
					header: {
						'Content-Type': 'application/x-www-form-urlencoded'
					},
					success: ((res) => {
						if (res.data.status === 200 && res.data.msg === 'OK') {
							this.hotSuperheroList = res.data.data
						} else {
							this.hotSuperheroList = []
						}
					}),
					fail: ((err) => {
						console.log(err)
						this.hotSuperheroList = []
					}),
					complete: (() => {
						this._getTrailderSuperheroData()
					})
				})
			},
			_getTrailderSuperheroData() {
				uni.request({
					url: common.api_base_url + '/index/movie/hot?type=trailer',
					method: 'POST',
					data: {
						qq: common.qq
					},
					header: {
						'Content-Type': 'application/x-www-form-urlencoded'
					},
					success: ((res) => {
						if (res.data.status === 200 && res.data.msg === 'OK') {
							this.trailerSuperheroList = res.data.data
						} else {
							this.trailerSuperheroList = []
						}
					}),
					fail: ((err) => {
						console.log(err)
						this.trailerSuperheroList = []
					}),
					complete: (() => {
						this._getPraiseData()
					})
				})
			},
			_getPraiseData() {
				uni.request({
					url: common.api_base_url + '/index/guessULike',
					method: 'POST',
					data: {
						qq: common.qq
					},
					header: {
						'Content-Type': 'application/x-www-form-urlencoded'
					},
					success: ((res) => {
						if (res.data.status === 200 && res.data.msg === 'OK') {
							this.praiseList = res.data.data
						} else {
							this.praiseList = []
						}
					}),
					fail: ((err) => {
						console.log(err)
						this.praiseList = []
					}),
					complete: (() => {
						uni.hideLoading()
						uni.stopPullDownRefresh()
					})
				})
			}
		},
		components: {
			uniRate
		}
	}
</script>
<style scoped>
	.index-page {
		width: 100%;
		background: rgb(248, 248, 248);
	}

	.carousel {
		width: 100%;
		height: 440upx;
	}

	.carousel image {
		width: 100%;
		height: 100%;
	}

	.page-block {
		background: #fff;
	}

	.super-hot {
		margin-top: 12upx;
		padding: 20upx;
	}

	.hot-title-waapper {
		display: flex;
		flex-direction: row;
		align-items: center;
	}

	.hot-ico {
		width: 30upx;
		height: 30upx;
	}

	.hot-title {
		font-size: 40upx;
		margin-left: 20upx;
		font-weight: bold;
	}

	.hot {
		width: 100%;
		white-space: nowrap;
	}

	.single-poster {
		display: inline-block;
		margin-left: 20upx;
	}

	.single-poster:last-child {
		margin-right: 20upx;
	}

	.poster-wrapper {
		display: flex;
		flex-direction: column;
	}

	.poster {
		width: 200upx;
		height: 270upx;
	}

	.movie-name {
		width: 200upx;
		margin-top: 10upx;
		font-size: 28upx;
		font-weight: bold;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.movie-score-wrapper {
		display: flex;
		flex-direction: row;
	}

	.star-ico {
		width: 20upx;
		height: 20upx;
		margin-top: 6upx;
	}

	.movie-score {
		font-size: 24upx;
		color: grey;
		margin-left: 8upx;
	}

	.hot-movies {
		display: flex;
		flex-direction: row;
		flex-wrap: wrap;
		justify-content: space-between;
		padding: 0 20upx 20upx 20upx;
	}

	.hot-movie-single {
		width: 350upx;
		height: 220upx;
		margin-top: 10upx;
	}

	.guess-u-like {
		display: flex;
		flex-direction: column;
	}

	.single-like-movie {
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		padding: 30upx 20upx;
	}

	.like-movie-img {
		width: 180upx;
		height: 240upx;
		border-radius: 3%;
	}

	.movie-desc {
		width: 340upx;
		display: flex;
		flex-direction: column;
	}

	.movie-title {
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.movie-info {
		color: #808080;
		font-size: 28upx;
	}

	.movie-oper {
		width: 140upx;
		display: flex;
		flex-direction: column;
		justify-content: center;
		border-left: dashed 4upx;
		border-left-color: #dbdbda;
	}

	.praise-ico {
		width: 40upx;
		height: 40upx;
		align-self: center;
	}

	.praise-me {
		font-size: 28upx;
		color: #feab2a;
		align-self: center;
	}

	.animation-opacity {
		font-weight: bold;
		opacity: 0;
	}
</style>
