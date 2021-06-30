<template>
	<view class="container">
		<view class="photocard" v-for="(photo,index) in photosarr" v-if="likesarr[index]" style="position: relative;">
			<image v-bind:src="photo" mode="" style="width: 100%;"></image>
			<image src="../../static/funcicon/like_hl.png" 
			style="position: absolute;width: 70rpx;height:70rpx;bottom: 40rpx;right: 40rpx;" 
			mode="" @click="dislike(index)"></image>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				likesarr:[],
				photoarr:[],
				index:0
			}
		},
		methods: {
			dislike(index){
				console.log(index)
				this.likesarr[index] = false
				this.$set(this.likesarr, index, false)
				uni.setStorage({
					key:'photo_like',
					data:this.likesarr
				})
			},
			
		},
		onLoad() {
			let imagesNameArr = [];
			let likearr = [];
			for (var i = 0; i < requireModule.keys().length; i++) {
			    imagesNameArr.push('../../static/photos/'+requireModule.keys()[i].substr(2, requireModule.keys()[i].length));
				likearr.push(false)
			}
			this.photosarr = imagesNameArr
			uni.getStorage({
				key:'photo_like',
				success:(res)=>{
					this.likesarr = res.data
				}
			})
			this.likesarr = likearr
			console.log(this.photosarr)
		},
	}
</script>

<style>
.photocard{
	width: 90%;
	margin-left: 5%;
	margin-top: 30rpx;
	border-radius: 20rpx;
	overflow: hidden;
	box-shadow: 10px 10px 5px #888888;
}
</style>
