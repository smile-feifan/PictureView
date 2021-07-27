<template>
	<view>
		<view class="home_tab">
			<view class="home_tab_title">
				<view class="title_inner">
					<uni-segmented-control :current="current" :values="items.map(v=>v.title)" @clickItem="onClickItem" styleType="text"
					 activeColor="#ff0000"></uni-segmented-control>
				</view>
				<navigator open-type="switchTab" url="/pages/search/index" class="iconfont iconsearch"></navigator>
			</view>
			<view class="home_tab_content">
				<view v-show="current === 0">
					<home-recommend></home-recommend>
				</view>
				<view v-show="current === 1">
					<home-category></home-category>
				</view>
				<view v-show="current === 2">
					<home-new></home-new>
				</view>
				<view v-show="current === 3">
					<home-album></home-album>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import homeAlbum from "./home-album"
	import homeCategory from "./home-category"
	import homeRecommend from "./home-recommend"
	import homeNew from "./home-new"
	export default {
		components: {
			homeAlbum,
			homeCategory,
			homeRecommend,
			homeNew
		},
		data() {
			return {
				items: [{
						title: "推荐"
					},
					{
						title: "分类"
					},
					{
						title: "最新"
					},
					{
						title: "专辑"
					}
				],
				current: 1
			};
		},
		methods: {
			onClickItem(e) {
				if (this.current !== e.currentIndex) {
					this.current = e.currentIndex;
				}
			},
			
			// 点击搜索
			// handleSearch(){
				
			// }
		},
	}
	
	
</script>

<style lang="scss">
.home_tab 
{
	.home_tab_title {
		position: relative;
		.title_inner{
			width: 60%;
			margin: 0 auto;
		}
		.iconsearch{
			position: absolute;
			top: 50%;
			transform: translateY(-50%);
			right: 5%;
		}
	}
	.home_tab_content{}
}
</style>
