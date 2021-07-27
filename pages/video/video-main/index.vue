<template>
	<scroll-view scroll-y="true" enable-flex class="video_main" @scrolltolower="handleScrolltolower">
		<view class="video_item"
		v-for="(item,index) in videowp"
		:key="item.id"
		@click="handleGoVideo(item)"
		>
		<image :src="item.img" mode="widthFix"></image>
		</view>
	</scroll-view>
</template>

<script>
	export default{
		props:{
			urlobj:Object
		},
		data() {
			return {
				videowp:[],
				hasMore:true
			}
		},
		mounted() {
			this.getList();
		},
		// 监听data 中数据的变化
		watch:{
			urlobj(){
				// console.log(this.urlobj);
				this.videowp=[];
				this.hasMore=true;
				this.getList();
			}
		},
		methods:{
			// 请求数据
		async	getList() {
				const res=await this.request({
					url:this.urlobj.url,
					data:this.urlobj.params
				});
				if(res.res.videowp.length===0){
					this.hasMore=false;
					uni.showToast({
						title:'哎呀，没有数据啦',
						icon:"none"
					});
					return;
				}
				this.videowp=[...this.videowp,...res.res.videowp];
			},
			
			// 分页加载
			handleScrolltolower(){
				console.log('触底啦')
				if(this.hasMore){
					this.urlobj.params.skip+=this.urlobj.params.limit;
					this.getList();
				}else{
					uni.showToast({
						title:'哎呀，没数据啦',
						icon:"none"
					})
				}
			},
			
			// 图片点击事件
			handleGoVideo(item) {
				// 1.将数据存到全局共享中
				getApp().globalData.video=item;
				// 2.页面跳转
				uni.navigateTo({
					url:"/pages/video-play/index"
				})
			}
		}
	}
</script>

<style lang="scss">
	.video_main{
		height: calc(100vh - 76rpx);
		display: flex;
		flex-wrap: wrap;
		.video_item{
			width: 33.33%;
			border: 5rpx solid #fff;
			image{}
		}
	}
</style>
