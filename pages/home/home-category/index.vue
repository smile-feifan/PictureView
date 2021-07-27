<template>
	<view class="home_category">
		<navigator 
		class="cate_item" 
		v-for="(item,index) in category" 
		:key="item.id" 
		:url="`/pages/img-category/index?id=${item.id}`">
			<image :src="item.cover" mode="widthFix"></image>
			<view class="cate_name">
				{{item.name}}
			</view>
		</navigator>
	</view>
</template>

<script>
	export default{data() {
		return {
			category:[]
		}
	},
		
		mounted(){
			// 修改页面标题
			uni.setNavigationBarTitle({
				title:'分类'
			}),
			this.getCateData();
		},
		methods:{
		async	getCateData(){
				const res = await this.request({
					url:'http://service.picasso.adesk.com/v1/vertical/category'
				});
		   this.category=res.res.category;
			}
		}
	}
</script>

<style lang="scss">
	.home_category{
		display: flex;
		flex-wrap: wrap;
		.cate_item{
			padding: 10rpx;
			width: 50%;
			position: relative;
			image{}
			.cate_name{
				position: absolute;
				width: 100%;
				height: 40rpx;
				bottom: 10rpx;
				left: 10rpx;
				color: #fff;
				font-size: 38rpx;
			  display: flex;
				align-items: center;
				padding-left: 20rpx;
				// css3的渐变
				background-image: linear-gradient(to right top,rgba(0,0,0,.2),rgba(0, 0,0,0));
			}
		}
			
		}
	
</style>
