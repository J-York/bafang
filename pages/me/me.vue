<template>
	<view class="container">
		<image src="../../static/Logo/BuildFor.png" mode="aspectFit"></image>
		<view class="navis">
			<navigator url="../my_favor/my_favor" class="navi_bar">收藏文章</navigator>
			<navigator url="../favor_photos/favor_photos" class="navi_bar">收藏图片</navigator>
			<navigator url="../about_us/about_us" class="navi_bar">关于我们</navigator>
		</view>
	</view>
	
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			jumpback(){
				uni.navigateBack();
			}
		}
	}
</script>

<style>
	.container{
		background-color: rgba(205, 205, 205, 0.2);
		height: 100%;
		margin-top: 0;
		width:100%;
		position: absolute;
	}
	.navis{
		width: 90%;
		margin-left: 2.5%;
		border-radius: 40rpx;
		box-shadow: 10px 10px 5px#C0C0C0;
		overflow: hidden;
		background-color: white;
	}
	.navi_bar{
		padding-left: 30rpx;
		padding-top: 30rpx;
		font-size: large;
		height: 80rpx;
		width: 100%;
		border-bottom: 1rpx solid #999999;
	}
</style>
