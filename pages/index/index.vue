<template>
	<view class="">
		<scroll-view scroll-y="true">
			<view class="container">
				<swiper :autoplay="true" :interval="3000" :duration="800" style="height: 400rpx;">
					<swiper-item v-for="(text,index) in texts.slice(0,3)" @click="choosecard(index)">
						<view class="swiper-item">
							<image v-bind:src="covers[index]" mode="aspectFit" style="position: relative;width: 100%;"></image>
							<cover-view class="cover_text">{{text.title}}</cover-view>
						</view>
					</swiper-item>
				</swiper>
				<view class="showpicker" style="position: relative;">
					<picker mode="selector" :range="showlist" @change="changepicker" :value="index">
						<view>{{showlist[index]}}&nbsp;◿</view>
					</picker>
				</view>
				<view v-for="(text,index) in texts">
					<uni-card class="card" isShadow="true" mode="style" @click="choosecard(index)"
					v-bind:title=text.title v-bind:thumbnail="covers[index]"
					 note="Tips" style="padding: 0;">
					 <view class="author" style="display: inline;font-size: small;">
					 	作者：{{text.author}}
					 </view>
					 <view class="time" style="display: inline;float: right;font-size: small;">
					 	发布时间：{{text.publish_time.substr(0,10)}}
					 </view>
						<template v-slot:footer>
							<view class="footer_box" style="display: flex;">
								<view style="width: 50%;text-align: center;">
									<image src="../../static/funcicon/hot.png" style="width: 40rpx;height: 40rpx;
									 padding-right: 10rpx;" mode=""></image>
									<span>热度: {{text.hot}}</span>
								</view>
								<view style="width: 50%;text-align: center;" @click="likeText(index)" v-if="!isLiked(index)">
									<image src="../../static/funcicon/like.png" style="width: 40rpx;height: 40rpx;
									 padding-right: 15rpx;" mode=""></image>
									<span>喜欢</span>
								</view>
								<view style="width: 50%;text-align: center;" @click="likeText(index)" v-if="isLiked(index)">
									<image src="../../static/funcicon/like_hl.png" style="width: 40rpx;height: 40rpx;
									 padding-right: 15rpx;" mode=""></image>
									<span>已收藏</span>
								</view>
							</view>
						</template>
					</uni-card>
				</view>				
			</view>
		</scroll-view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello',
				texts:[],
				covers:[],
				pic:'https://6865-hello-cloudbase-9gcpcck0b8a5c821-1305327613.tcb.qcloud.la/users/picture/JYork.jpg',
				user_like:[],
				showlist:['默认顺序','热度最高','最新发布'],
				index:0
			}
		},
		async onLoad() {
			let that = this
			uni.showLoading({
				title:'加载中',
				mask:true
			})
			uni.request({
				method:'POST',
				url:'https://hello-cloudbase-9gcpcck0b8a5c821-1305327613.ap-shanghai.app.tcloudbase.com/getalltext',
				success: (res) => {
					that.texts = res.data.data
					var coverID = []
					for(var i=0;i<that.texts.length;i++){
						coverID.push(that.texts[i].cover)
					}
					this.getCovers(coverID)
				},
				complete: () => {
					uni.hideLoading()
				}
			})
		},
		onShow() {
			let that = this
			uni.getStorage({
				key:'user_like',
				success:(res)=>{
					that.user_like = res.data
				}
			})
		},
		methods: {
			async getCovers(coversID){
				let that = this
				await uni.request({
					method:"POST",
					url:"https://hello-cloudbase-9gcpcck0b8a5c821-1305327613.ap-shanghai.app.tcloudbase.com/getpicid",
					data:{
						coverid:coversID
					},
					success: (res) => {
						var coverURL = []
						var fileList = res.data.fileList
						for(var i=0;i<fileList.length;i++){
							that.covers.push(fileList[i].download_url)
						}
					}
				})
			},
			changepicker(event){
				this.index = event.detail.value
				if(this.index==1){
					this.texts.sort(this.comparebyHot).reverse()
				}
				if(this.index==2){
					this.texts.sort(this.comparebyTime).reverse()
				}
			},
			choosecard(index){
				let that = this
				uni.navigateTo({
					url:'../textdetail/textdetail'
				})
				setTimeout(()=>{
					uni.$emit('openPage',{
						data:that.texts[index],
						cover:that.covers[index],
						liked:that.isLiked(index)
					})
				},20)
			},
			comparebyTime(obj1,obj2){
				var val1 = obj1.publish_time
				var val2 = obj2.publish_time
				if (val1 < val2) {
					return -1;
				} else if (val1 > val2) {
					return 1;
				} else {
					return 0;
				}      
			},
			comparebyHot(obj1,obj2){
				var val1 = obj1.hot
				var val2 = obj2.hot
				if (val1 < val2) {
					return -1;
				} else if (val1 > val2) {
					return 1;
				} else {
					return 0;
				}      
			},
			likeText(index){
				let that = this
				if (this.isLiked(index)){
					this.user_like.splice(this.user_like.indexOf(this.texts[index]._id),1)
				}
				else{
					this.user_like.push(this.texts[index]._id)
				}
				uni.setStorage({
					key:'user_like',
					data:that.user_like
				})
			},
			isLiked(index){
				// console.log(this.texts[index]._id in this.user_like)
				// return this.texts[index]._id in this.user_like
				for(var i=0;i<this.user_like.length;i++){
					if(this.texts[index]._id == this.user_like[i]){
						return true
					}
				}
				return false
			},
		}
	}
</script>

<style>
	.card_cov{
		margin: 0;
	}
	.card_text{
		margin: 0;
	}
	.card_img{
		width: 100%;
	}
	.text_cover{
		font-size: large;
		font-weight: bold;
	}
	.cover_text{
		position: absolute;
		height: 60rpx;
		bottom: 0rpx;
		padding-left: 30rpx;
		width: 100%;
		padding-top: 20rpx;
		background-color: rgba(125,125,125,0.6);
		font-size:large;
		color: white;
	}
	.showpicker{
		padding-left: 40rpx;
		font-size: large;
		background-color: #f3f3f3;
		height: 60rpx;
		padding-top: 10rpx;
	}
</style>