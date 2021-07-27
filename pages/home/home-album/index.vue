<template>
	<scroll-view scroll-y class="album_scroll_view" @scrolltolower="handleToLower">
		<!-- 轮播图开始 -->
		<view class="album_swiper">
			<swiper indicator-dots circular autoplay>
				<swiper-item>
					<image src="https://pan.zealsay.com/zealsay/cover/4.jpg" mode="widthFix"></image>
				</swiper-item>
				<swiper-item>
					<image src="https://pan.zealsay.com/zealsay/cover/1.jpg" mode="widthFix"></image>
				</swiper-item>
				<swiper-item>
					<image src="https://pan.zealsay.com/zealsay/cover/6.jpg" mode="widthFix"></image>
				</swiper-item>
			</swiper>
		</view>
		<!-- 轮播图结束 -->

		<!-- 列表开始 -->
		<view class="album_list">
			<navigator class="album_item" v-for="(item,index) in album" :key="item.id"
			:url="`/pages/album/index?id=${item.id}`">
				<view class="album_img">
					<image mode="aspectFill" :src="item.cover"></image>
				</view>
				<view class="album_info">
					<view class="album_name">
						{{item.name}}
					</view>
					<view class="album_desc">
						{{item.desc}}
					</view>
					<view class="album_btn">
						<view class="album_attention"> + 关注</view>
					</view>
				</view>
			</navigator>
		</view>
		<!-- 列表结束 -->
	</scroll-view>
</template>

<script>
	export default {
		data() {
			return {
				// 请求参数
				params: {
					limit: 30,
					order: "new",
					skip: 0
				},
				//轮播图数组
				banner: [],
				// 列表数组
				album: [],
				// 是否有分页数据
				hasMore: true

			}
		},
		mounted() {
			// 修改页面标题
			uni.setNavigationBarTitle({
					title: "专辑"
				}),
				this.getList()
		},
		methods: {
			// 上拉加载事件
			handleToLower(e) {
				if (this.hasMore) {
					this.params.skip += this.params.limit;
					this.getList();
				} else {
					uni.showToast({
						title: '`(*>﹏<*)′ 数据到底了哦',
						icon: "none"
					})
				}
			},
			getList() {
				this.request({
						url: "http://service.picasso.adesk.com/v1/wallpaper/album",
						data: this.params
					})
					.then(res => {
						if (this.banner.length === 0) {
							this.banner = res.res.banner;
						}
						if (res.res.album.length === 0) {
							this.hasMore = false;
							return;
						}
						this.album = [...this.album, ...res.res.album];
						// 将专辑数据缓存下来
						getApp().globalData.album=this.album;
						const album=this.album;
						uni.setStorageSync('album',album);
					})
			}
		}
	}
</script>

<style lang="scss">
	.album_scroll_view {
		height: calc(100vh - 36rpx);
	}

	.album_swiper {

		// 图片的宽高比例 640/284
		// 750rpx/height=640/284
		swiper {
			height: calc(750rpx / 2.3);

			image {
				height: 100%;
			}
		}
	}

	.album_list {
		padding: 10rpx;

		.album_item {
			padding: 10rpx 0;
			display: flex;
			border-bottom: 1rpx solid #ccc;

			.album_img {
				flex: 1;
				padding: 10rpx;

				image {
					width: 200rpx;
					height: 200rpx;
				}
			}

			.album_info {
				flex: 2;
				padding: 0 10rpx;
				overflow: hidden;

				.album_name {
					font-size: 30rpx;
					color: #000;
					padding: 10rpx;
				}

				.album_desc {
					padding: 10rpx 0;
					font-size: 24rpx;

					text-overflow: ellipsis;
					overflow: hidden;
					white-space: nowrap;
				}

				.album_btn {
					padding: 10rpx;
					display: flex;
					justify-content: flex-end;

					.album_attention {
						color: $color;
						border: 1rpx solid $color;
						padding: 10rpx;
					}
				}
			}

		}
	}
</style>
