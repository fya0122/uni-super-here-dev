<template>
	<view class="page">
		<view class="search-block">
			<view class="search-ico-wrapper">
				<image class="search-ico" src="../../static/icos/search.png"></image>
			</view>
			<input type="text" placeholder="搜索预告" maxlength="10" class="search-text"/>
		</view>
		<view class="movie-list page-block">
			<view class="movie-wrapper">
				<image class="poster" src=""></image>
			</view>
			<view class="movie-wrapper">
				<image class="poster" src=""></image>
			</view>
			<view class="movie-wrapper">
				<image class="poster" src=""></image>
			</view>
			<view class="movie-wrapper">
				<image class="poster" src=""></image>
			</view>
			<view class="movie-wrapper">
				<image class="poster" src=""></image>
			</view>
			<view class="movie-wrapper">
				<image class="poster" src=""></image>
			</view>
			<view class="movie-wrapper">
				<image class="poster" src=""></image>
			</view>
			<view class="movie-wrapper">
				<image class="poster" src=""></image>
			</view>
		</view>
	</view>
</template>

<script>
	export default {}
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
		background: red;
	}
</style>
