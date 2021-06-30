<template>
	<view>
		<scroll-view scroll-y="true" >
			<view class="text_cover">
				<image v-bind:src="text_cover" mode="" style="width: 100%;"></image>
			</view>
			<view class="text_title">
				{{text_title}}
			</view>
			<view class="author_info" style="display: flex;">
				<image class="author_cover" v-bind:src="author_pic" mode=""></image>
				<view>
					<view class="author_name">{{text_author}}</view>
					<view class="create_time">{{text_date}}</view>
				</view>
			</view>
			<view class="text_content">
				<rich-text v-bind:nodes="text_content"></rich-text>
			</view>
		</scroll-view>
		<view class="like_button" @click="likeText()">
			<image src="../../static/funcicon/like.png" class="like_pic" mode="" v-if="!is_liked"></image>
			<image src="../../static/funcicon/like_hl.png" class="like_pic" mode="" v-if="is_liked"></image>
		</view>
		
	</view>
</template>
<script>
	export default{
		async onLoad(){
			uni.showLoading()
			let that = this
			await uni.$on('openPage',(info)=>{
				this.loadtext(info.data,info.cover)
				that.is_liked = info.liked
			})
			uni.hideLoading()
		},
		onUnload(){
			uni.$off('openPage')
		},
		data(){
			return{
				text_title:'None',
				text_author:'None',
				text_cover:'None',
				text_content:'None',
				text_date:'None',
				is_liked:false,
				text_id:'None',
				author_pic:"https://6865-hello-cloudbase-9gcpcck0b8a5c821-1305327613.tcb.qcloud.la/users/picture/JYork.jpg",
			}
		},
		methods:{
			async loadtext(text,cover){
				this.text_title = text.title
				this.text_author = text.author
				this.text_cover = cover
				this.text_content = text.context
				this.text_date = text.publish_time.substr(0,10)
				this.text_id = text._id
			},
			likeText(){
				let that = this
				this.is_liked = !this.is_liked
				if(!this.is_liked){
					uni.getStorage({
						key:'user_like',
						success:function(res){
							var tuser_like = res.data
							tuser_like.splice(tuser_like.indexOf(that.text_id))
							uni.setStorage({
								key:'user_like',
								data:tuser_like
							})
						}
					})
				}
				if(this.is_liked){
					uni.getStorage({
						key:'user_like',
						success:function(res){
							var tuser_like = res.data
							tuser_like.push(that.text_id)
							uni.setStorage({
								key:'user_like',
								data:tuser_like
							})
						}
					})
				}
			}
		}
	}
</script>
<style>
	.text_title{
		padding-top: 20rpx;
		padding-left: 20rpx;
		font-size: x-large;
		font-weight: 800;
	}
	.text_cover{
		height: 300rpx;
		overflow:hidden;
	}
	.author_info{
		padding-top:20rpx;
		height: 150rpx;
		padding-left: 20rpx;
		margin-bottom: 40rpx;
	}
	.author_cover{
		height: 150rpx;
		width: 150rpx;
		border-radius: 50%;
		margin-right: 30rpx;
	}
	.author_name{
		height: 80rpx;
		padding-top: 20rpx;
		padding-left: 20rpx;
		font-size: larger;
		font-weight: bold;
	}
	.create_time{
		height: 50rpx;
		padding-left: 20rpx;
		color: #555555;
	}
	.text_content{
		padding-left: 30rpx;
		padding-right: 30rpx;
		font-size: medium;
	}
	.like_button{
		width: 100rpx;
		height: 100rpx;
		border-radius: 50%;
		position: fixed;
		bottom: 200rpx;
		right: 60rpx;
		background-color: #ffb9ba;
		text-align: center;
	}
	.like_pic{
		padding-top: 18rpx;
		width: 70rpx;
		height: 70rpx;
	}
</style>