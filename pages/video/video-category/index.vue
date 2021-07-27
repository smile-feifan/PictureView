<template>
	<view class="video_cate">
		<navigator class="cate_item" v-for="(item,index) in cateObj" :key="item.id" :url="`/pages/img-category/index?id=${item.id}`">
			<image :src="item.cover" mode="widthFix"></image>
			<view class="cate_name">
				{{item.name}}
			</view>
		</navigator>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				cateObj: []
			}
		},
		mounted() {
			this.getList();
		},
		methods:{
			// 获取分类数据
		async	getList(){
				const res = await this.request({
					url:'http://service.picasso.adesk.com/v1/vertical/category'
				});
				this.cateObj=res.res.category;
			}
		}
	}
</script>

<style lang="scss">
	.video_cate{
		display: flex;
		flex-wrap: wrap;
		.cate_item{
			width: 50%;
			border: 5rpx solid #fff;
			position: relative;
			image{
				
			}
			.cate_name{
				position: absolute;
				left: 0rpx;
				bottom: 0rpx;
				width: 100%;
				height: 60rpx;
				color: #fff;
				font-size: 38rpx;
				background-image: linear-gradient(to right top,rgba(0,0,0,.3),rgba(0,0,0,.1));
				display: flex;
				align-items: center;
				padding-left: 20rpx;
			}
		}
	}
</style>
