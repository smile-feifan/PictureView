<template>
	<scroll-view @scrolltolower="handleToLower" scroll-y class="recommend_view" v-if="recommend.length>0" @scroll="handleScroll"
	 :scroll-top="top">
		<view class="scroll_top" @click="backTop" v-show="isShow">
			<image src="../../../static/icon/backTop2.png" mode="widthFix"></image>
		</view>
		<!-- 推荐开始 -->
		<view class="recommend_wrap">
			<navigator class="recommend_item" v-for="(item,index) in recommend" :key="item.id" :url="`/pages/album/index?id=${item.target}`">
				<image :src="item.thumb" mode="widthFix"></image>
			</navigator>
		</view>
		<!-- 推荐结束 -->

		<!-- 月份 开始 -->
		<view class="months_wrap">
			<view class="months_title">
				<view class="months_title_info">
					<view class="months_info">
						<text> {{months.DD}} / </text>
						{{months.MM}}月
					</view>
					<view class="months_text">
						满怀希望便会所向披靡
					</view>
				</view>
				<view class="months_title_more">
					更多 >
				</view>
			</view>
			<view class="months_content">
				<view class="months_item" v-for="(item,index) in months.imgscr" :key="index">
					<image mode="aspectFill" :src=item></image>
				</view>
			</view>
		</view>
		<!-- 月份 结束 -->

		<!-- 热门 开始 -->
		<view class="hots_wrap">
			<view class="hots_title">
				<text>热门</text>
			</view>
			<view class="hots_content">
				<view class="hots_item" v-for="(item,index) in hots" :key="item.id">
					<go-detail :list="hots" :index="index">
						<image :src="item.thumb" mode="widthFix"></image>
					</go-detail>
				</view>
			</view>
		</view>
		<!-- 热门 结束 -->
	</scroll-view>
</template>

<script>
	import moment from "@/utils/moment.js"
	import goDetail from "@/components/go-detail/go-detail.vue"
	export default {
		components: {
			goDetail
		},
		data() {
			return {
				// 推荐列表
				recommend: [],
				// 月份
				months: {},
				src: [
					'https://pan.zealsay.com/zealsay/cover/7.jpg',
					'https://pan.zealsay.com/zealsay/cover/1.jpg',
					'https://pan.zealsay.com/zealsay/cover/2.jpg',
					'https://pan.zealsay.com/zealsay/cover/3.jpg',
					'https://pan.zealsay.com/zealsay/cover/4.jpg',
					'https://pan.zealsay.com/zealsay/cover/5.jpg',
				],
				hots: [],
				// 请求的参数
				params: {
					limit: 30,
					adult: false,
					first: 1,
					skip: 0,
					order: "hot"
				},
				// 是否还有下一页
				hasMore: true,
				// 返回顶部
				top: 0,
				isShow: false
			}
		},
		mounted() {
			// 修改标题
			uni.setNavigationBarTitle({
					title: "推荐"
				}),
				this.request({
					url: "http://157.122.54.189:9088/image/v3/homepage/vertical",
					data: {
						// 获取多少条数据
						limit: 30,
						// 关键字
						order: "hot",
						// 跳过多少条
						skip: "0"
					}
				})
				.then(res => {
					// 推荐模块
					this.recommend = res.res.homepage[1].items;
					// 月份模块
					this.months = res.res.homepage[2];
					// 将时间戳 改成 18号/月 moment.js
					this.months.MM = moment(this.months.stime).format("MM");
					this.months.DD = moment(this.months.stime).format("DD");
					this.months.imgscr = this.src;
				}),
				this.getList();
		},
		methods: {
			// 点击返回顶部
			backTop() {
				this.top = 0;
				this.isShow = false;
			},
			// 监听scroll-view滚动
			handleScroll(e) {
				this.top = e.detail.scrollTop;
				if (this.top > 200) {
					this.isShow = true;
				}
			},
			// 获取接口数据
			getList() {
				this.request({
						url: "http://service.picasso.adesk.com/v1/vertical/vertical",
						data: this.params
					})
					.then(res => {
						// 判断还有没有下一页数据
						if (res.res.vertical.length === 0) {
							this.hasMore = false;
							return;
						}
						//热门模块
						// 使用数组拼接 es6
						this.hots = [...this.hots, ...res.res.vertical];
					})
			},
			// 滚动条触底事件
			handleToLower() {
				/* 
				1.修改参数  skip+=limit
				2.重新发送请求 getList()
				3.请求回来成功 hots 数据叠加
				 */
				if (this.hasMore) {
					this.params.skip += this.params.limit;
					this.getList();
				} else {
					// 弹窗提示用户
					uni.showToast({
						title: "哎呀 `(*>﹏<*)′ 到底了了哦",
						icon: "none"
					})
				}
			}
		}
	}
</script>

<style lang="scss">
	.recommend_view {
		// height:屏幕的高度 - 头部标题的高度
		height: calc(100vh - 66rpx);
	}

	.recommend_wrap {
		display: flex;
		flex-wrap: wrap;

		.recommend_item {
			width: 50%;
			border: 3rpx solid #fff;
		}
	}

	.months_wrap {
		.months_title {
			display: flex;
			justify-content: space-between;
			padding: 20rpx;

			.months_title_info {
				color: $color;
				font-size: 30rpx;
				font-weight: 600;
				display: flex;

				.months_info {
					text {
						font-size: 36rpx;
					}
				}

				.months_text {
					font-size: 34rpx;
					color: #666;
					margin-left: 30rpx;

				}
			}

			.months_title_more {
				font-size: 34rpx;
				color: $color;
			}
		}

		.months_content {
			display: flex;
			flex-wrap: wrap;

			.months_item {
				width: 33.33%;
			}
		}

	}

	.hots_wrap {
		.hots_title {
			padding: 20rpx;

			text {
				border-left: 20rpx solid $color;
				font-size: 34rpx;
				font-weight: 600;
				padding-left: 20rpx;
			}
		}

		.hots_content {
			display: flex;
			flex-wrap: wrap;

			.hots_item {
				width: 33.33%;
				border: 5rpx solid #fff;

				image {}
			}
		}
	}

	.scroll_top {
		width: 100rpx;
		height: 100rpx;
		position: fixed;
		right: 0;
		bottom: 0;
	}
</style>
