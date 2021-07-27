<template>
	<scroll-view scroll-y enable-flex class="home_new">
		<view open-type="redirect" class="new_item" v-for="(item,index) in newData" :key="item.id">
			<go-detail :list="newData" :index="index">
				<image :src="item.thumb" mode="widthFix"></image>
			</go-detail>
		</view>
	</scroll-view>
</template>

<script>
	import goDetail from "@/components/go-detail/go-detail.vue"
	export default {
		components: {
			goDetail
		},
		data() {
			return {
				newData: [],
				params: {
					limit: 30,
					skip: 0,
					order: 'new'
				}
			}
		},
		mounted() {
			// 修改页面标题
			uni.setNavigationBarTitle({
					title: '最新'
				}),

				// 获取数据
				this.getList();

		},
		methods: {
			async getList() {
				const res = await this.request({
					url: "http://service.picasso.adesk.com/v1/vertical/vertical",
					data: this.params
				});
				this.newData = res.res.vertical;
			}
		}
	}
</script>
<style lang="scss">
	.home_new {
		height: calc(100vh - 76rpx);
		display: flex;
		flex-wrap: wrap;

		.new_item {
			width: 33.33%;
			border: 5rpx solid #fff;

			image {}
		}
	}
</style>
