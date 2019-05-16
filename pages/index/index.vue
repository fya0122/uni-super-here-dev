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
				hotSuperheroList: []
			}
		},
		onLoad() {
			this._getSwiperData()
		},
		methods: {
			_getSwiperData() {
				uni.showLoading({
					title: '加载中'
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
						uni.hideLoading()
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
		height: 100%;
		position: absolute;
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
		font-size: 12upx;
		color: grey;
		margin-left: 8upx;
	}
</style>
