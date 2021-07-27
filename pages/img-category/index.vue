<template>
	<view>
		<view class="cate_tab">
			<view class="cate_tab_title">
				<view class="title_inner">
					<uni-segmented-control :current="current" :values="items.map(v=>v.title)" @clickItem="onClickItem"
						styleType="text" activeColor="#ff0000"></uni-segmented-control>
				</view>
				<view class="iconfont iconsearch"></view>
			</view>
			<scroll-view @scrolltolower="handleScrolltolower" enable-flex scroll-y class="cate_tab_content">
				<view class="cate_item" v-for="(item,index) in vertical" :key="item.id">
					<image :src="item.thumb" mode="widthFix"></image>
				</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				items: [{
						title: "最新",
						order: "new"
					},
					{
						title: "热门",
						order: "hot"
					}
				],
				current: 0,
				params: {
					limit: 30,
					skip: 0,
					order: "new"
				},
				id: 0,
				// 页面显示数据的数组
				vertical: [],
				// 有没有下一页数据
				hasMore:true
			};
		},
		onLoad(options) {
			this.id = options.id,
				this.getList();
		},
		methods: {
			onClickItem(e) {
				// 1.根据被点击的标题切换标题
				// 2.切换order
				if (this.current !== e.currentIndex) {
					this.current = e.currentIndex;
				}else{
					// 点击的是相同的标题
					return;
				}
				this.params.order = this.items[e.currentIndex].order;
				this.params.skip=0;
				this.vertical=[];
				this.getList();
			},
			// 获取数据
			async getList() {
				const res = await this.request({
					url: `http://service.picasso.adesk.com/v1/vertical/category/${this.id}/vertical`,
					data: this.params
				});
				if(res.res.vertical.length===0){
					this.hasMore=false;
					uni.showToast({
						title:'没有更多数据啦',
						icon:"none"
					})
					return;
				}
				this.vertical = [...this.vertical,...res.res.vertical];
			},
			// 加载下一页数据
			handleScrolltolower(){
				if(this.hasMore){
					this.params.skip+=this.params.limit;
					this.getList();
				}else{
					uni.showToast({
						title:'没有更多数据啦',
						icon:'none'
					})
				}
				
			}
		}
	}
</script>

<style lang="scss">
	.cate_tab {
		.cate_tab_title {
			position: relative;

			.title_inner {
				width: 60%;
				margin: 0 auto;
			}

			.iconsearch {
				position: absolute;
				top: 50%;
				transform: translateY(-50%);
				right: 5%;
			}
		}

		.cate_tab_content {
			height: calc(100vh - 72rpx);
			display: flex;
			flex-wrap: wrap;

			.cate_item {
				width: 33.33%;
				border: 5rpx solid #fff;

				image {}
			}
		}
	}
</style>
