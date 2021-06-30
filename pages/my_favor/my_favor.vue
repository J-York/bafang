<template>
	<view class="">
		<scroll-view scroll-y="true">
			<view class="container">
				<view v-for="(text,index) in texts">
					<uni-card v-show="isLiked(index)" class="card" isShadow="true" mode="title" @click="choosecard(index)"
					v-bind:title=text.author thumbnail="https://6865-hello-cloudbase-9gcpcck0b8a5c821-1305327613.tcb.qcloud.la/users/picture/JYork.jpg" 
					extra="详情 > " note="Tips">
						<view class="card_cov">
							<image class="card_img" v-bind:src="covers[index]" mode="aspectFill" style="position: relative;">
								<cover-view class="text_cover">{{text.title}}</cover-view>
							</image>
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
				user_like:[]
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
</style>