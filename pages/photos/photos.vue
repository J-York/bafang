<template>
	<scroll-view scroll-y="true" >
		<view class="container">
			<view class="leftpanel">
				<view class="photocard" v-for="(photo,index) in photourls" v-if="index%2==0">
					<image v-bind:src="photo" mode="aspectFill" class="photos" @click="reviewphoto(index)" ></image>
					<view style="position: absolute;right: 20rpx;bottom: 30rpx;" v-if="!isliked(index)" @click="like(index)">
						<image src="../../static/funcicon/like.png" style="width: 60rpx;height:60rpx" mode=""></image>
					</view>
					<view style="position: absolute;right: 20rpx;bottom: 30rpx;" v-if="isliked(index)" @click="dislike(index)">
						<image src="../../static/funcicon/like_hl.png" style="width: 60rpx;height:60rpx" mode=""></image>
					</view>
				</view>
			</view>
			<view class="rightpanel">
				<view class="photocard" v-for="(photo,index) in photourls" v-if="index%2==1">
					<image v-bind:src="photo" mode="aspectFill" class="photos" @click="reviewphoto(index)"></image>
					<view style="position: absolute;right: 20rpx;bottom: 30rpx;" v-if="!isliked(index)" @click="like(index)">
						<image src="../../static/funcicon/like.png" style="width: 60rpx;height:60rpx" mode=""></image>
					</view>
					<view style="position: absolute;right: 20rpx;bottom: 30rpx;" v-if="isliked(index)" @click="dislike(index)">
						<image src="../../static/funcicon/like_hl.png" style="width: 60rpx;height:60rpx" mode=""></image>
					</view>
				</view>
			</view>
		</view>
	</scroll-view>
</template>
<script>
	export default{
		async onLoad() {
			let that = this
			uni.request({
				method:'POST',
				url:'https://hello-cloudbase-9gcpcck0b8a5c821-1305327613.ap-shanghai.app.tcloudbase.com/getallphoto',
				success: (res) => {
					var photos = res.data.data
					for(var i=0;i<res.data.data.length;i++){
						that.photoids.push(photos[i]._id)
						that.photourls.push(photos[i].url)
						that.likesarr.push(false)
					}
				},
				complete: () => {
					uni.hideLoading()
				}
			})
			uni.getStorage({
				key:'photo_like',
				success:(res)=>{
					that.likesarr = res.data
				}
			})
			this.imagesArr = []
		},
		data(){
			return{
				photosarr:[],
				likesarr:[],
				photourls:[],
				photoids:[]
			}
		},
		methods:{
			reviewphoto(index){
				uni.previewImage({
					current:0,
					urls:[this.photosarr[index]]
				})
			},
			like(index){
				this.$set(this.likesarr, index, true)
				uni.setStorage({
					key:'photo_like',
					data:this.likesarr
				})
			},
			dislike(index){
				this.likesarr[index] = false
				this.$set(this.likesarr, index, false)
				uni.setStorage({
					key:'photo_like',
					data:this.likesarr
				})
			},
			isliked(index){
				return this.likesarr[index] == true
			}
		}
	}
</script>

<style>
.container{
	background-color: #f4f4f4;
	padding-left: 2%;
}
.leftpanel{
	width: 50%;
	float: left;
	background-color: #f4f4f4;
}
.rightpanel{
	width: 50%;
	float: right;
	background-color: #f4f4f4;
}
.photos{
	width: 100%;
}
.photocard{
	background-color: #FFFFFF;
	width: 96%;
	margin-top: 15rpx;
	border-radius: 15rpx;
	overflow: hidden;
	padding: 0px;
	margin-bottom: 0px;
	position: relative;
}
.card_footer{
	background-color: #FFFFFF;
	padding-bottom: 15rpx;
	padding-left: 15rpx;
	font-size: large;
}
</style>
