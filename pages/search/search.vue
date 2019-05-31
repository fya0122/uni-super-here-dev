<template>
	<view class="page">
		<view class="search-block">
			<view class="search-ico-wrapper">
				<image class="search-ico" src="../../static/icos/search.png"></image>
			</view>
			<input @confirm="searchMe" confirm-type="search" type="text" placeholder="搜索预告" maxlength="10" class="search-text" />
		</view>
		<view class="movie-list page-block">
			<view :key="item.id" v-for="item in rowsList" class="movie-wrapper">
				<image class="poster" :src="item.cover"></image>
			</view>
		</view>
	</view>
</template>

<script>
	import common from '../../common/common.js'
	export default {
		data() {
			return {
				rowsList: [],
				myKeywords: '', // 搜索的关键字
				page: 1, // 当前第几页
				totalPages: 0 // 总页数
			}
		},
		onLoad() {
			this._getSearchPageData()
		},
		methods: {
			_getSearchPageData() {
				uni.showLoading({
					title: '加载中',
					mask: true
				});
				uni.request({
					url: common.api_base_url + '/search/list?keywords=&page=&pageSize=',
					header: {
						'Content-Type': 'application/x-www-form-urlencoded'
					},
					data: {
						qq: common.qq
					},
					method: 'POST',
					success: ((res) => {
						if (res.data.status === 200 && res.data.msg === 'OK') {
							this.rowsList = res.data.data.rows
						} else {
							this.rowsList = []
						}
					}),
					fail: ((err) => {
						console.log(err)
						this.rowsList = []
					}),
					complete: (() => {
						uni.hideLoading()
					})
				})
			},
			searchMe(e) {
				this.myKeywords = e.detail.value
				this.rowsList = []
				this.pagedTrailerList(this.myKeywords, 1, 15)
			},
			pagedTrailerList(keywords, page, pageSize) {
				uni.showLoading({
					title: '加载中',
					mask: true
				});
				uni.request({
					url: common.api_base_url + '/search/list?keywords=' + keywords + '&page=' + page + '&pageSize=' + pageSize,
					header: {
						'Content-Type': 'application/x-www-form-urlencoded'
					},
					data: {
						qq: common.qq
					},
					method: 'POST',
					success: ((res) => {
						if (res.data.status === 200 && res.data.msg === 'OK') {
							const templateData = res.data.data.rows
							this.rowsList = this.rowsList.concat(templateData)
							this.totalPages = res.data.data.total
							this.page = page
						}
					}),
					fail: ((err) => {
						console.log(err)
					}),
					complete: (() => {
						uni.hideLoading()
					})
				})
			}
		}
	}
</script>

<style scoped>
	.page {
		width: 100%;
		height: 100%;
		background: #F7F7F7;
	}

	.search-block {
		display: flex;
		flex-direction: row;
		align-items: center;
		padding: 0 20upx 20upx 20upx;
		/* 固定下搜索栏 */
		position: fixed;
		top: 100;
		z-index: 5000;
		background-color: #f8f8f8;
	}

	.search-ico-wrapper {
		background-color: #EAEAEA;
		height: 60upx;
		display: flex;
		align-items: center;
		padding: 0 10upx;
	}

	.search-ico {
		width: 40upx;
		height: 40upx;
	}

	.search-text {
		font-size: 28upx;
		background-color: #eaeaea;
		height: 60upx;
		width: 650upx;
	}

	.movie-list {
		display: flex;
		flex-direction: row;
		justify-content: flex-start;
		flex-wrap: wrap;
		padding: 100upx 10upx 0 10upx;
	}

	.page-block {
		background-color: #FFFFFF;
	}

	.movie-wrapper {
		padding: 10upx 20upx;
	}

	.poster {
		width: 200upx;
		height: 270upx;
		background: #eee;
	}
</style>
